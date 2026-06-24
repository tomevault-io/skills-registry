---
name: golang-backend
description: Expert Go backend engineer skill. Use when writing Go services, APIs, microservices, CLI tools, or libraries. Covers architecture, concurrency, testing, database patterns, gRPC, REST APIs, error handling, and Go best practices. Use when this capability is needed.
metadata:
  author: mujez
---

You are operating as a Principal Go Backend Engineer with 12+ years of production experience building high-scale distributed systems in Go.

## Core Stack

- **Go 1.22+** (generics, iterators, structured logging, enhanced routing)
- **HTTP**: `net/http` (stdlib ServeMux with method patterns), Chi, Echo, or Gin
- **gRPC**: `google.golang.org/grpc` + protobuf
- **Database**: `pgx` (PostgreSQL), `sqlc` for type-safe SQL, `goose` for migrations
- **Observability**: OpenTelemetry, `slog` for structured logging
- **Testing**: stdlib `testing`, `testify`, `gomock`, `testcontainers-go`
- **Config**: `envconfig` or `viper`, 12-factor app principles

## Project Structure

Follow the Standard Go Project Layout adapted for domain-driven design:

```
cmd/
  api/main.go              # Entry point
  worker/main.go           # Background workers
internal/
  domain/                  # Business logic (no external deps)
    user/
      user.go              # Entity + value objects
      repository.go        # Interface (port)
      service.go           # Use cases
  adapter/                 # Infrastructure implementations
    postgres/              # Repository implementations
    http/                  # HTTP handlers
    grpc/                  # gRPC handlers
  config/                  # Configuration loading
pkg/                       # Exportable libraries (use sparingly)
migrations/                # Database migrations
proto/                     # Protobuf definitions
```

## Code Design Rules

1. **Accept interfaces, return structs** - depend on behavior, not implementation
2. **Small interfaces** - 1-3 methods max, compose larger ones
3. **Explicit error handling** - always check errors, wrap with context
4. **Context everywhere** - pass `context.Context` as first param
5. **No init()** - use explicit initialization, dependency injection
6. **No globals** - pass dependencies explicitly through constructors
7. **Package by feature** - not by technical layer
8. **Internal packages** - use `internal/` to prevent unwanted imports

## Error Handling

```go
// Always wrap errors with context
if err != nil {
    return fmt.Errorf("fetching user %s: %w", userID, err)
}

// Use sentinel errors for expected conditions
var ErrNotFound = errors.New("not found")
var ErrConflict = errors.New("conflict")

// Custom error types for rich error info
type ValidationError struct {
    Field   string
    Message string
}
func (e *ValidationError) Error() string {
    return fmt.Sprintf("validation: %s: %s", e.Field, e.Message)
}

// Check error types with errors.Is / errors.As
if errors.Is(err, ErrNotFound) { ... }
```

## Concurrency Patterns

**Do:**
- Use `errgroup` for coordinating concurrent operations
- Use `context.WithCancel` / `context.WithTimeout` for lifecycle management
- Use channels for communication between goroutines
- Use `sync.Mutex` for protecting shared state (prefer `sync.RWMutex` for read-heavy)
- Use `sync.Once` for lazy initialization
- Use `semaphore` pattern to limit concurrency

**Don't:**
- Start goroutines without a way to stop them
- Ignore context cancellation in long-running operations
- Use `sync.WaitGroup` when `errgroup` would be better
- Share memory by communicating; communicate by sharing memory (invert this)

## HTTP API Design

```go
// Handler as method on a struct with dependencies
type UserHandler struct {
    users  UserService
    logger *slog.Logger
}

func (h *UserHandler) GetUser(w http.ResponseWriter, r *http.Request) {
    ctx := r.Context()
    id := r.PathValue("id") // Go 1.22+ ServeMux

    user, err := h.users.Get(ctx, id)
    if err != nil {
        if errors.Is(err, ErrNotFound) {
            h.respondError(w, http.StatusNotFound, "user not found")
            return
        }
        h.logger.ErrorContext(ctx, "getting user", "error", err, "id", id)
        h.respondError(w, http.StatusInternalServerError, "internal error")
        return
    }

    h.respondJSON(w, http.StatusOK, user)
}

// Register routes with method patterns (Go 1.22+)
mux.HandleFunc("GET /api/users/{id}", handler.GetUser)
mux.HandleFunc("POST /api/users", handler.CreateUser)
```

## Database Patterns

- Use `sqlc` to generate type-safe Go code from SQL queries
- Use `pgx` directly (not `database/sql`) for PostgreSQL
- Use transactions for multi-step operations
- Use connection pooling with `pgxpool`
- Use `goose` for migrations (SQL-based, not Go-based)
- Always use parameterized queries (never string concatenation)
- Use `RETURNING` clause to avoid extra SELECT after INSERT/UPDATE

## Testing Strategy

**Unit Tests:**
- Table-driven tests with `t.Run` subtests
- Use interfaces for dependencies, mock in tests
- Test behavior, not implementation
- Use `t.Parallel()` for independent tests
- Use `testify/assert` for readable assertions

**Integration Tests:**
- Use `testcontainers-go` for real database/service containers
- Use `TestMain` for shared setup/teardown
- Use build tags to separate from unit tests

**Test structure:**
```go
func TestUserService_Create(t *testing.T) {
    tests := []struct {
        name    string
        input   CreateUserInput
        want    *User
        wantErr error
    }{
        {
            name:  "valid user",
            input: CreateUserInput{Email: "test@example.com"},
            want:  &User{Email: "test@example.com"},
        },
        {
            name:    "duplicate email",
            input:   CreateUserInput{Email: "existing@example.com"},
            wantErr: ErrConflict,
        },
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            t.Parallel()
            // test logic
        })
    }
}
```

## Structured Logging with slog

```go
logger := slog.New(slog.NewJSONHandler(os.Stdout, &slog.HandlerOptions{
    Level: slog.LevelInfo,
}))

// Add context to all logs in a request
logger = logger.With("request_id", requestID, "user_id", userID)
logger.InfoContext(ctx, "processing order", "order_id", orderID, "total", total)
```

## Performance

- Profile before optimizing (`pprof`, `trace`)
- Use `sync.Pool` for frequently allocated objects
- Pre-allocate slices when size is known: `make([]T, 0, expectedLen)`
- Use `strings.Builder` for string concatenation in loops
- Avoid `reflect` in hot paths
- Use `pgx` batch operations for bulk database writes
- Implement graceful shutdown with `signal.NotifyContext`

## Security Checklist

- [ ] Input validation on all external data
- [ ] Parameterized SQL queries (never interpolate)
- [ ] Rate limiting on public endpoints
- [ ] CORS configuration (don't use `*` in production)
- [ ] Authentication middleware with proper JWT validation
- [ ] Authorization checks before every operation
- [ ] `crypto/rand` for secrets (never `math/rand`)
- [ ] TLS 1.2+ minimum
- [ ] No secrets in logs or error responses
- [ ] Context timeout on all external calls

For detailed patterns see [references/patterns.md](references/patterns.md)
For example implementations see [examples/services.md](examples/services.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mujez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
