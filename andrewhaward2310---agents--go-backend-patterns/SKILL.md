---
name: go-backend-patterns
description: | Use when this capability is needed.
metadata:
  author: AndrewHaward2310
---

# Go Backend Patterns

Production patterns for Go backend services, microservices, and CLI tools.
Covers the full stack from project structure through deployment.

---

## Project Structure

### Standard Layout

```
myservice/
├── cmd/
│   ├── server/
│   │   └── main.go          # HTTP/gRPC server entry point
│   └── worker/
│       └── main.go          # Background worker entry point
├── internal/
│   ├── config/
│   │   └── config.go        # Configuration loading
│   ├── domain/
│   │   ├── models.go        # Domain types
│   │   └── errors.go        # Domain errors
│   ├── handler/
│   │   ├── handler.go       # HTTP handlers
│   │   └── middleware.go    # HTTP middleware
│   ├── repository/
│   │   ├── postgres.go      # Database implementation
│   │   └── repository.go   # Repository interface
│   ├── service/
│   │   └── service.go       # Business logic
│   └── worker/
│       └── consumer.go      # Message consumers
├── pkg/
│   ├── httputil/
│   │   └── response.go      # Shared HTTP utilities
│   └── logger/
│       └── logger.go        # Logging setup
├── api/
│   └── proto/
│       └── service.proto     # Protobuf definitions
├── migrations/
│   ├── 001_initial.up.sql
│   └── 001_initial.down.sql
├── deployments/
│   ├── Dockerfile
│   └── k8s/
│       ├── deployment.yaml
│       └── service.yaml
├── go.mod
├── go.sum
└── Makefile
```

### Key Principles

- `cmd/`: Each subdirectory is a separate binary. Keep main.go thin — wire dependencies and start.
- `internal/`: Private application code. Cannot be imported by other modules.
- `pkg/`: Public library code that other projects can import. Use sparingly.
- `api/`: API definitions — protobuf, OpenAPI specs, JSON schemas.
- Never put business logic in handlers. Handlers translate HTTP ↔ domain types.

---

## HTTP Service Patterns

### Server with Graceful Shutdown

```go
package main

import (
    "context"
    "fmt"
    "net/http"
    "os"
    "os/signal"
    "syscall"
    "time"

    "github.com/go-chi/chi/v5"
    "github.com/go-chi/chi/v5/middleware"
)

func main() {
    cfg := config.Load()
    logger := setupLogger(cfg.LogLevel)

    // Dependency injection
    db := repository.NewPostgres(cfg.DatabaseURL)
    svc := service.New(db, logger)
    handler := handler.New(svc, logger)

    r := chi.NewRouter()

    // Middleware stack — order matters
    r.Use(middleware.RequestID)
    r.Use(middleware.RealIP)
    r.Use(LoggingMiddleware(logger))
    r.Use(middleware.Recoverer)
    r.Use(middleware.Timeout(30 * time.Second))

    // Routes
    r.Route("/api/v1", func(r chi.Router) {
        r.Use(AuthMiddleware(cfg.JWTSecret))
        r.Get("/records", handler.ListRecords)
        r.Post("/records", handler.CreateRecord)
        r.Get("/records/{id}", handler.GetRecord)
        r.Put("/records/{id}", handler.UpdateRecord)
        r.Delete("/records/{id}", handler.DeleteRecord)
    })

    // Health checks — no auth
    r.Get("/health", handler.HealthCheck)
    r.Get("/ready", handler.ReadinessCheck)

    srv := &http.Server{
        Addr:         fmt.Sprintf(":%d", cfg.Port),
        Handler:      r,
        ReadTimeout:  15 * time.Second,
        WriteTimeout: 30 * time.Second,
        IdleTimeout:  60 * time.Second,
    }

    // Graceful shutdown
    go func() {
        logger.Info("starting server", "port", cfg.Port)
        if err := srv.ListenAndServe(); err != http.ErrServerClosed {
            logger.Error("server error", "err", err)
            os.Exit(1)
        }
    }()

    quit := make(chan os.Signal, 1)
    signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
    <-quit

    logger.Info("shutting down server")
    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
    defer cancel()

    if err := srv.Shutdown(ctx); err != nil {
        logger.Error("forced shutdown", "err", err)
    }
}
```

### Middleware Patterns

```go
// Logging middleware with structured logging
func LoggingMiddleware(logger *slog.Logger) func(http.Handler) http.Handler {
    return func(next http.Handler) http.Handler {
        return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            start := time.Now()
            ww := middleware.NewWrapResponseWriter(w, r.ProtoMajor)

            defer func() {
                logger.Info("request completed",
                    "method", r.Method,
                    "path", r.URL.Path,
                    "status", ww.Status(),
                    "bytes", ww.BytesWritten(),
                    "duration_ms", time.Since(start).Milliseconds(),
                    "request_id", middleware.GetReqID(r.Context()),
                )
            }()

            next.ServeHTTP(ww, r)
        })
    }
}

// Auth middleware extracting JWT claims into context
func AuthMiddleware(secret string) func(http.Handler) http.Handler {
    return func(next http.Handler) http.Handler {
        return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            token := extractBearerToken(r)
            if token == "" {
                http.Error(w, `{"error":"unauthorized"}`, http.StatusUnauthorized)
                return
            }

            claims, err := validateJWT(token, secret)
            if err != nil {
                http.Error(w, `{"error":"invalid token"}`, http.StatusUnauthorized)
                return
            }

            ctx := context.WithValue(r.Context(), userClaimsKey, claims)
            next.ServeHTTP(w, r.WithContext(ctx))
        })
    }
}

// Rate limiting middleware
func RateLimitMiddleware(rps int) func(http.Handler) http.Handler {
    limiter := rate.NewLimiter(rate.Limit(rps), rps*2)
    return func(next http.Handler) http.Handler {
        return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            if !limiter.Allow() {
                http.Error(w, `{"error":"rate limit exceeded"}`, http.StatusTooManyRequests)
                return
            }
            next.ServeHTTP(w, r)
        })
    }
}
```

### Handler Pattern

```go
type Handler struct {
    svc    *service.Service
    logger *slog.Logger
}

func New(svc *service.Service, logger *slog.Logger) *Handler {
    return &Handler{svc: svc, logger: logger}
}

func (h *Handler) GetRecord(w http.ResponseWriter, r *http.Request) {
    id := chi.URLParam(r, "id")

    record, err := h.svc.GetRecord(r.Context(), id)
    if err != nil {
        switch {
        case errors.Is(err, domain.ErrNotFound):
            respondError(w, http.StatusNotFound, "record not found")
        default:
            h.logger.Error("failed to get record", "id", id, "err", err)
            respondError(w, http.StatusInternalServerError, "internal error")
        }
        return
    }

    respondJSON(w, http.StatusOK, record)
}

func respondJSON(w http.ResponseWriter, status int, data any) {
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(status)
    json.NewEncoder(w).Encode(data)
}

func respondError(w http.ResponseWriter, status int, message string) {
    respondJSON(w, status, map[string]string{"error": message})
}
```

---

## gRPC Patterns

### Protobuf Definition

```protobuf
syntax = "proto3";
package myservice.v1;
option go_package = "myservice/api/proto/v1";

service RecordService {
    rpc GetRecord(GetRecordRequest) returns (GetRecordResponse);
    rpc ListRecords(ListRecordsRequest) returns (ListRecordsResponse);
    rpc StreamUpdates(StreamRequest) returns (stream RecordUpdate);
}

message GetRecordRequest {
    string id = 1;
}

message GetRecordResponse {
    Record record = 1;
}

message Record {
    string id = 1;
    string name = 2;
    map<string, string> metadata = 3;
    int64 created_at = 4;
}
```

### Server with Interceptors

```go
import (
    "google.golang.org/grpc"
    "google.golang.org/grpc/codes"
    "google.golang.org/grpc/status"
)

func main() {
    srv := grpc.NewServer(
        grpc.ChainUnaryInterceptor(
            LoggingInterceptor(logger),
            RecoveryInterceptor(),
            AuthInterceptor(jwtSecret),
        ),
        grpc.ChainStreamInterceptor(
            StreamLoggingInterceptor(logger),
        ),
    )

    pb.RegisterRecordServiceServer(srv, &recordServer{svc: svc})

    lis, _ := net.Listen("tcp", ":50051")
    srv.Serve(lis)
}

// Unary interceptor for logging
func LoggingInterceptor(logger *slog.Logger) grpc.UnaryServerInterceptor {
    return func(ctx context.Context, req any, info *grpc.UnaryServerInfo,
        handler grpc.UnaryHandler) (any, error) {
        start := time.Now()
        resp, err := handler(ctx, req)
        logger.Info("grpc call",
            "method", info.FullMethod,
            "duration_ms", time.Since(start).Milliseconds(),
            "error", err,
        )
        return resp, err
    }
}

// Server-side streaming
func (s *recordServer) StreamUpdates(
    req *pb.StreamRequest,
    stream pb.RecordService_StreamUpdatesServer,
) error {
    ch := s.svc.Subscribe(req.Filter)
    defer s.svc.Unsubscribe(ch)

    for {
        select {
        case update := <-ch:
            if err := stream.Send(update); err != nil {
                return status.Error(codes.Internal, "stream send failed")
            }
        case <-stream.Context().Done():
            return nil
        }
    }
}
```

---

## Concurrency Patterns

### Worker Pool

```go
func WorkerPool[T any, R any](
    ctx context.Context,
    workers int,
    jobs <-chan T,
    process func(context.Context, T) (R, error),
) <-chan Result[R] {
    results := make(chan Result[R], workers)

    var wg sync.WaitGroup
    for i := 0; i < workers; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            for {
                select {
                case job, ok := <-jobs:
                    if !ok {
                        return
                    }
                    val, err := process(ctx, job)
                    results <- Result[R]{Value: val, Err: err}
                case <-ctx.Done():
                    return
                }
            }
        }()
    }

    go func() {
        wg.Wait()
        close(results)
    }()

    return results
}

type Result[T any] struct {
    Value T
    Err   error
}
```

### errgroup for Parallel Tasks

```go
import "golang.org/x/sync/errgroup"

func FetchAll(ctx context.Context, ids []string) (map[string]*Record, error) {
    g, ctx := errgroup.WithContext(ctx)
    g.SetLimit(10) // max 10 concurrent goroutines

    var mu sync.Mutex
    results := make(map[string]*Record, len(ids))

    for _, id := range ids {
        g.Go(func() error {
            record, err := fetchRecord(ctx, id)
            if err != nil {
                return fmt.Errorf("fetch %s: %w", id, err)
            }
            mu.Lock()
            results[id] = record
            mu.Unlock()
            return nil
        })
    }

    if err := g.Wait(); err != nil {
        return nil, err
    }
    return results, nil
}
```

### Fan-Out / Fan-In

```go
func FanOutFanIn(ctx context.Context, input <-chan Event) <-chan ProcessedEvent {
    // Fan out to N workers
    const numWorkers = 5
    channels := make([]<-chan ProcessedEvent, numWorkers)
    for i := 0; i < numWorkers; i++ {
        channels[i] = processEvents(ctx, input)
    }

    // Fan in — merge all worker outputs
    return merge(ctx, channels...)
}

func merge[T any](ctx context.Context, channels ...<-chan T) <-chan T {
    out := make(chan T)
    var wg sync.WaitGroup

    for _, ch := range channels {
        wg.Add(1)
        go func() {
            defer wg.Done()
            for {
                select {
                case val, ok := <-ch:
                    if !ok {
                        return
                    }
                    select {
                    case out <- val:
                    case <-ctx.Done():
                        return
                    }
                case <-ctx.Done():
                    return
                }
            }
        }()
    }

    go func() {
        wg.Wait()
        close(out)
    }()

    return out
}
```

---

## Database Patterns

### Repository with pgx

```go
import (
    "context"
    "github.com/jackc/pgx/v5/pgxpool"
)

type PostgresRepo struct {
    pool *pgxpool.Pool
}

func NewPostgresRepo(ctx context.Context, connString string) (*PostgresRepo, error) {
    config, err := pgxpool.ParseConfig(connString)
    if err != nil {
        return nil, fmt.Errorf("parse config: %w", err)
    }

    config.MaxConns = 25
    config.MinConns = 5
    config.MaxConnLifetime = 30 * time.Minute
    config.MaxConnIdleTime = 5 * time.Minute

    pool, err := pgxpool.NewWithConfig(ctx, config)
    if err != nil {
        return nil, fmt.Errorf("create pool: %w", err)
    }

    return &PostgresRepo{pool: pool}, nil
}

func (r *PostgresRepo) GetRecord(ctx context.Context, id string) (*Record, error) {
    var rec Record
    err := r.pool.QueryRow(ctx,
        `SELECT id, name, data, created_at, updated_at
         FROM records WHERE id = $1`, id,
    ).Scan(&rec.ID, &rec.Name, &rec.Data, &rec.CreatedAt, &rec.UpdatedAt)

    if err != nil {
        if errors.Is(err, pgx.ErrNoRows) {
            return nil, domain.ErrNotFound
        }
        return nil, fmt.Errorf("query record %s: %w", id, err)
    }
    return &rec, nil
}

// Transactions
func (r *PostgresRepo) TransferCredits(
    ctx context.Context, fromID, toID string, amount int,
) error {
    tx, err := r.pool.Begin(ctx)
    if err != nil {
        return fmt.Errorf("begin tx: %w", err)
    }
    defer tx.Rollback(ctx)

    var balance int
    err = tx.QueryRow(ctx,
        "SELECT balance FROM accounts WHERE id = $1 FOR UPDATE", fromID,
    ).Scan(&balance)
    if err != nil {
        return fmt.Errorf("get balance: %w", err)
    }

    if balance < amount {
        return domain.ErrInsufficientBalance
    }

    _, err = tx.Exec(ctx,
        "UPDATE accounts SET balance = balance - $1 WHERE id = $2", amount, fromID)
    if err != nil {
        return fmt.Errorf("debit: %w", err)
    }

    _, err = tx.Exec(ctx,
        "UPDATE accounts SET balance = balance + $1 WHERE id = $2", amount, toID)
    if err != nil {
        return fmt.Errorf("credit: %w", err)
    }

    return tx.Commit(ctx)
}
```

### Migrations with golang-migrate

```go
import (
    "github.com/golang-migrate/migrate/v4"
    _ "github.com/golang-migrate/migrate/v4/database/postgres"
    _ "github.com/golang-migrate/migrate/v4/source/file"
)

func RunMigrations(dbURL, migrationsPath string) error {
    m, err := migrate.New(
        "file://"+migrationsPath,
        dbURL,
    )
    if err != nil {
        return fmt.Errorf("create migrator: %w", err)
    }

    if err := m.Up(); err != nil && !errors.Is(err, migrate.ErrNoChange) {
        return fmt.Errorf("run migrations: %w", err)
    }

    version, dirty, _ := m.Version()
    slog.Info("migrations complete", "version", version, "dirty", dirty)
    return nil
}
```

---

## Error Handling

### Sentinel Errors and Wrapping

```go
package domain

import (
    "errors"
    "fmt"
)

// Sentinel errors — compare with errors.Is
var (
    ErrNotFound            = errors.New("not found")
    ErrAlreadyExists       = errors.New("already exists")
    ErrInsufficientBalance = errors.New("insufficient balance")
    ErrUnauthorized        = errors.New("unauthorized")
)

// Custom error type with context
type ValidationError struct {
    Field   string
    Message string
}

func (e *ValidationError) Error() string {
    return fmt.Sprintf("validation error on %s: %s", e.Field, e.Message)
}

// Wrapping with %w for error chain
func (s *Service) GetRecord(ctx context.Context, id string) (*Record, error) {
    record, err := s.repo.GetRecord(ctx, id)
    if err != nil {
        return nil, fmt.Errorf("service.GetRecord(%s): %w", id, err)
    }
    return record, nil
}

// Checking wrapped errors
func handleError(err error) int {
    switch {
    case errors.Is(err, domain.ErrNotFound):
        return http.StatusNotFound
    case errors.Is(err, domain.ErrUnauthorized):
        return http.StatusUnauthorized
    default:
        var ve *domain.ValidationError
        if errors.As(err, &ve) {
            return http.StatusBadRequest
        }
        return http.StatusInternalServerError
    }
}
```

---

## Configuration

### Structured Config with envconfig

```go
package config

import (
    "fmt"
    "time"

    "github.com/kelseyhightower/envconfig"
)

type Config struct {
    // Server
    Port         int           `envconfig:"PORT" default:"8080"`
    ReadTimeout  time.Duration `envconfig:"READ_TIMEOUT" default:"15s"`
    WriteTimeout time.Duration `envconfig:"WRITE_TIMEOUT" default:"30s"`

    // Database
    DatabaseURL     string `envconfig:"DATABASE_URL" required:"true"`
    MaxDBConns      int    `envconfig:"MAX_DB_CONNS" default:"25"`

    // Auth
    JWTSecret    string        `envconfig:"JWT_SECRET" required:"true"`
    TokenExpiry  time.Duration `envconfig:"TOKEN_EXPIRY" default:"24h"`

    // Observability
    LogLevel     string `envconfig:"LOG_LEVEL" default:"info"`
    OTELEndpoint string `envconfig:"OTEL_ENDPOINT" default:""`

    // Feature flags
    EnableMetrics bool `envconfig:"ENABLE_METRICS" default:"true"`
}

func Load() (*Config, error) {
    var cfg Config
    if err := envconfig.Process("APP", &cfg); err != nil {
        return nil, fmt.Errorf("load config: %w", err)
    }
    return &cfg, nil
}
```

---

## Testing

### Table-Driven Tests

```go
func TestParseAmount(t *testing.T) {
    tests := []struct {
        name    string
        input   string
        want    int
        wantErr bool
    }{
        {name: "valid integer", input: "100", want: 100},
        {name: "valid with cents", input: "99.99", want: 9999},
        {name: "zero", input: "0", want: 0},
        {name: "negative", input: "-50", want: 0, wantErr: true},
        {name: "invalid", input: "abc", want: 0, wantErr: true},
        {name: "empty", input: "", want: 0, wantErr: true},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got, err := ParseAmount(tt.input)
            if (err != nil) != tt.wantErr {
                t.Errorf("ParseAmount(%q) error = %v, wantErr %v", tt.input, err, tt.wantErr)
                return
            }
            if got != tt.want {
                t.Errorf("ParseAmount(%q) = %d, want %d", tt.input, got, tt.want)
            }
        })
    }
}
```

### Integration Testing with testcontainers

```go
import (
    "context"
    "testing"

    "github.com/testcontainers/testcontainers-go"
    "github.com/testcontainers/testcontainers-go/modules/postgres"
)

func TestPostgresRepo(t *testing.T) {
    if testing.Short() {
        t.Skip("skipping integration test")
    }

    ctx := context.Background()

    pgContainer, err := postgres.Run(ctx,
        "postgres:16-alpine",
        postgres.WithDatabase("testdb"),
        postgres.WithUsername("test"),
        postgres.WithPassword("test"),
    )
    if err != nil {
        t.Fatal(err)
    }
    defer pgContainer.Terminate(ctx)

    connStr, _ := pgContainer.ConnectionString(ctx, "sslmode=disable")

    repo, err := NewPostgresRepo(ctx, connStr)
    if err != nil {
        t.Fatal(err)
    }

    // Run tests against real database
    t.Run("CreateAndGet", func(t *testing.T) {
        record := &Record{ID: "test-1", Name: "Test"}
        err := repo.Create(ctx, record)
        assert.NoError(t, err)

        got, err := repo.GetRecord(ctx, "test-1")
        assert.NoError(t, err)
        assert.Equal(t, "Test", got.Name)
    })
}
```

### Mocking with Interfaces

```go
// Define interface in the consumer package, not the provider
type RecordRepository interface {
    GetRecord(ctx context.Context, id string) (*Record, error)
    ListRecords(ctx context.Context, filter Filter) ([]*Record, error)
    Create(ctx context.Context, record *Record) error
}

// Mock implementation for testing
type MockRepo struct {
    GetRecordFunc   func(ctx context.Context, id string) (*Record, error)
    ListRecordsFunc func(ctx context.Context, filter Filter) ([]*Record, error)
    CreateFunc      func(ctx context.Context, record *Record) error
}

func (m *MockRepo) GetRecord(ctx context.Context, id string) (*Record, error) {
    return m.GetRecordFunc(ctx, id)
}

// Usage in tests
func TestServiceGetRecord(t *testing.T) {
    mock := &MockRepo{
        GetRecordFunc: func(ctx context.Context, id string) (*Record, error) {
            if id == "exists" {
                return &Record{ID: "exists", Name: "Found"}, nil
            }
            return nil, domain.ErrNotFound
        },
    }

    svc := service.New(mock, slog.Default())

    t.Run("found", func(t *testing.T) {
        rec, err := svc.GetRecord(context.Background(), "exists")
        assert.NoError(t, err)
        assert.Equal(t, "Found", rec.Name)
    })

    t.Run("not found", func(t *testing.T) {
        _, err := svc.GetRecord(context.Background(), "nope")
        assert.ErrorIs(t, err, domain.ErrNotFound)
    })
}
```

---

## Observability

### Structured Logging with slog

```go
import "log/slog"

func SetupLogger(level string) *slog.Logger {
    var logLevel slog.Level
    switch level {
    case "debug":
        logLevel = slog.LevelDebug
    case "warn":
        logLevel = slog.LevelWarn
    case "error":
        logLevel = slog.LevelError
    default:
        logLevel = slog.LevelInfo
    }

    handler := slog.NewJSONHandler(os.Stdout, &slog.HandlerOptions{
        Level:     logLevel,
        AddSource: true,
    })

    logger := slog.New(handler)
    slog.SetDefault(logger)
    return logger
}

// Usage — always use structured fields
logger.Info("processing request",
    "method", r.Method,
    "path", r.URL.Path,
    slog.Group("user",
        "id", userID,
        "role", role,
    ),
)

// Context-aware logging
func (s *Service) Process(ctx context.Context, id string) error {
    logger := s.logger.With("operation", "process", "id", id)
    logger.InfoContext(ctx, "starting processing")
    // ...
    logger.InfoContext(ctx, "processing complete", "duration_ms", elapsed)
    return nil
}
```

### OpenTelemetry Tracing

```go
import (
    "go.opentelemetry.io/otel"
    "go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracegrpc"
    "go.opentelemetry.io/otel/sdk/trace"
    sdkresource "go.opentelemetry.io/otel/sdk/resource"
    semconv "go.opentelemetry.io/otel/semconv/v1.26.0"
)

func InitTracer(ctx context.Context, serviceName, endpoint string) (func(), error) {
    exporter, err := otlptracegrpc.New(ctx,
        otlptracegrpc.WithEndpoint(endpoint),
        otlptracegrpc.WithInsecure(),
    )
    if err != nil {
        return nil, err
    }

    res := sdkresource.NewWithAttributes(
        semconv.SchemaURL,
        semconv.ServiceNameKey.String(serviceName),
    )

    tp := trace.NewTracerProvider(
        trace.WithBatcher(exporter),
        trace.WithResource(res),
        trace.WithSampler(trace.TraceIDRatioBased(0.1)), // sample 10%
    )
    otel.SetTracerProvider(tp)

    return func() { tp.Shutdown(context.Background()) }, nil
}

// Usage in service code
var tracer = otel.Tracer("myservice")

func (s *Service) ProcessOrder(ctx context.Context, orderID string) error {
    ctx, span := tracer.Start(ctx, "ProcessOrder")
    defer span.End()

    span.SetAttributes(attribute.String("order.id", orderID))

    // Child span for database call
    ctx, dbSpan := tracer.Start(ctx, "db.GetOrder")
    order, err := s.repo.GetOrder(ctx, orderID)
    dbSpan.End()

    if err != nil {
        span.RecordError(err)
        return err
    }

    // ...
    return nil
}
```

---

## Message Queue Patterns

### NATS Consumer

```go
import "github.com/nats-io/nats.go"

func SetupNATS(url string) (*nats.Conn, error) {
    nc, err := nats.Connect(url,
        nats.RetryOnFailedConnect(true),
        nats.MaxReconnects(-1),
        nats.ReconnectWait(2*time.Second),
        nats.DisconnectErrHandler(func(nc *nats.Conn, err error) {
            slog.Warn("nats disconnected", "err", err)
        }),
        nats.ReconnectHandler(func(nc *nats.Conn) {
            slog.Info("nats reconnected", "url", nc.ConnectedUrl())
        }),
    )
    return nc, err
}

// JetStream consumer with durable subscription
func ConsumeOrders(ctx context.Context, nc *nats.Conn, handler OrderHandler) error {
    js, _ := nc.JetStream()

    sub, err := js.Subscribe("orders.>",
        func(msg *nats.Msg) {
            var order Order
            if err := json.Unmarshal(msg.Data, &order); err != nil {
                slog.Error("unmarshal order", "err", err)
                msg.Nak()
                return
            }

            if err := handler.Process(ctx, &order); err != nil {
                slog.Error("process order", "id", order.ID, "err", err)
                msg.NakWithDelay(5 * time.Second)
                return
            }

            msg.Ack()
        },
        nats.Durable("order-processor"),
        nats.ManualAck(),
        nats.AckWait(30*time.Second),
        nats.MaxDeliver(5),
    )
    if err != nil {
        return err
    }

    <-ctx.Done()
    return sub.Drain()
}
```

---

## Caching

### Redis with go-redis

```go
import "github.com/redis/go-redis/v9"

type Cache struct {
    client *redis.Client
    ttl    time.Duration
}

func NewCache(addr string, ttl time.Duration) *Cache {
    client := redis.NewClient(&redis.Options{
        Addr:         addr,
        PoolSize:     50,
        MinIdleConns: 10,
        ReadTimeout:  3 * time.Second,
        WriteTimeout: 3 * time.Second,
    })
    return &Cache{client: client, ttl: ttl}
}

// Cache-aside pattern
func (c *Cache) GetOrFetch(
    ctx context.Context,
    key string,
    fetch func() (any, error),
) (any, error) {
    // Try cache first
    val, err := c.client.Get(ctx, key).Result()
    if err == nil {
        var result any
        if err := json.Unmarshal([]byte(val), &result); err == nil {
            return result, nil
        }
    }

    // Cache miss — fetch from source
    result, err := fetch()
    if err != nil {
        return nil, err
    }

    // Store in cache (fire-and-forget)
    if data, err := json.Marshal(result); err == nil {
        c.client.Set(ctx, key, data, c.ttl)
    }

    return result, nil
}
```

---

## Deployment

### Docker Multi-Stage Build

```dockerfile
# Build stage
FROM golang:1.23-alpine AS builder

RUN apk add --no-cache git ca-certificates

WORKDIR /build
COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build \
    -ldflags="-s -w -X main.version=$(git describe --tags)" \
    -o /app ./cmd/server

# Runtime stage
FROM alpine:3.20

RUN apk add --no-cache ca-certificates tzdata
RUN addgroup -S app && adduser -S app -G app

COPY --from=builder /app /usr/local/bin/app
COPY migrations/ /migrations/

USER app
EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s \
    CMD wget -qO- http://localhost:8080/health || exit 1

ENTRYPOINT ["app"]
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myservice
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myservice
  template:
    metadata:
      labels:
        app: myservice
    spec:
      containers:
        - name: myservice
          image: myregistry/myservice:latest
          ports:
            - containerPort: 8080
          env:
            - name: DATABASE_URL
              valueFrom:
                secretKeyRef:
                  name: myservice-secrets
                  key: database-url
          resources:
            requests:
              memory: "128Mi"
              cpu: "100m"
            limits:
              memory: "512Mi"
              cpu: "500m"
          livenessProbe:
            httpGet:
              path: /health
              port: 8080
            initialDelaySeconds: 5
            periodSeconds: 15
          readinessProbe:
            httpGet:
              path: /ready
              port: 8080
            initialDelaySeconds: 3
            periodSeconds: 5
```

---

## CLI Patterns

### Cobra Command Structure

```go
import "github.com/spf13/cobra"

var rootCmd = &cobra.Command{
    Use:   "mytool",
    Short: "Production backend management tool",
}

var serveCmd = &cobra.Command{
    Use:   "serve",
    Short: "Start the HTTP server",
    RunE: func(cmd *cobra.Command, args []string) error {
        port, _ := cmd.Flags().GetInt("port")
        return startServer(port)
    },
}

var migrateCmd = &cobra.Command{
    Use:   "migrate [up|down]",
    Short: "Run database migrations",
    Args:  cobra.ExactArgs(1),
    RunE: func(cmd *cobra.Command, args []string) error {
        dbURL, _ := cmd.Flags().GetString("database-url")
        return runMigrations(dbURL, args[0])
    },
}

func init() {
    serveCmd.Flags().IntP("port", "p", 8080, "server port")
    migrateCmd.Flags().String("database-url", "", "database connection URL")
    migrateCmd.MarkFlagRequired("database-url")

    rootCmd.AddCommand(serveCmd, migrateCmd)
}
```

---

## Go vs Rust: When to Use Which

| Aspect | Go | Rust |
|--------|-----|------|
| I/O-bound services | Excellent — goroutines are cheap | Good but more verbose |
| CPU-intensive work | Adequate with goroutines | Superior — zero-cost abstractions |
| Memory safety | GC handles it | Compile-time guarantees, no GC |
| Build speed | Very fast (<10s typical) | Slow (minutes for large projects) |
| Deployment | Single static binary | Single static binary |
| Learning curve | Low — productive in days | Steep — weeks to months |
| Concurrency model | CSP (goroutines + channels) | async/await + ownership |
| FFI/Systems programming | cgo is awkward | Native and ergonomic |
| Team scaling | Easy onboarding | Harder to hire, slower onboarding |

**Choose Go when**: Building HTTP/gRPC services, microservices with high I/O concurrency,
DevOps tooling, or when team velocity matters more than raw performance.

**Choose Rust when**: Building database engines, compilers, cryptographic systems,
embedded systems, or when deterministic performance and memory control are critical.

---
> Source: [AndrewHaward2310/.agents](https://github.com/AndrewHaward2310/.agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
