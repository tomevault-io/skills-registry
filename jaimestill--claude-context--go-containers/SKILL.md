---
name: go-containers
description: > Use when this capability is needed.
metadata:
  author: jaimestill
---

# Go Containers & Infrastructure

## When This Skill Applies

- Creating or editing Dockerfiles
- Integrating external binaries (ImageMagick, ffmpeg, etc.)
- Setting up deployment configuration
- Verifying external dependencies

## Principles

### 1. External Binary Integration

Leverage mature, cross-platform tools via `os/exec` rather than reimplementing.

```go
import "os/exec"

// Check availability at startup, not first use
func CheckDependencies() error {
    required := []string{"magick", "ffmpeg", "gs"}

    for _, bin := range required {
        if _, err := exec.LookPath(bin); err != nil {
            return fmt.Errorf("%s not installed: %w", bin, err)
        }
    }
    return nil
}

// Safe command execution
func RunCommand(ctx context.Context, name string, args ...string) ([]byte, error) {
    cmd := exec.CommandContext(ctx, name, args...)

    output, err := cmd.CombinedOutput()
    if err != nil {
        return nil, fmt.Errorf("command %s failed: %w\noutput: %s", name, err, output)
    }

    return output, nil
}
```

### 2. Containerization for External Dependencies

Document and implement container requirements for external tools.

```dockerfile
# Multi-stage build for Go with external dependencies
FROM golang:1.25-alpine AS builder

# Install build dependencies
RUN apk add --no-cache git

WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 go build -o /service ./cmd/service

# Runtime image with external tools
FROM alpine:3.19

# Install runtime dependencies
RUN apk add --no-cache \
    imagemagick \
    ghostscript \
    ca-certificates \
    tzdata

# Copy binary from builder
COPY --from=builder /service /service

# Verify dependencies at container start
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s \
    CMD /service health || exit 1

ENTRYPOINT ["/service"]
```

### 3. Dependency Verification at Startup

Fail fast if required dependencies are missing.

```go
func main() {
    // Check dependencies before starting
    if err := verifyDependencies(); err != nil {
        log.Fatalf("dependency check failed: %v", err)
    }

    // Continue with normal startup
    if err := run(); err != nil {
        log.Fatalf("application error: %v", err)
    }
}

func verifyDependencies() error {
    deps := map[string]string{
        "magick":      "ImageMagick",
        "gs":          "Ghostscript",
        "pdftotext":   "Poppler",
    }

    var missing []string
    for cmd, name := range deps {
        if _, err := exec.LookPath(cmd); err != nil {
            missing = append(missing, fmt.Sprintf("%s (%s)", name, cmd))
        }
    }

    if len(missing) > 0 {
        return fmt.Errorf("missing dependencies: %s", strings.Join(missing, ", "))
    }
    return nil
}
```

## Patterns

### Dockerfile Best Practices

```dockerfile
# Use specific versions, not :latest
FROM golang:1.25.2-alpine3.19 AS builder

# Separate dependency download from code copy
COPY go.mod go.sum ./
RUN go mod download

# Copy code after dependencies (better layer caching)
COPY . .

# Build with optimizations
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -ldflags="-w -s" -o /app ./cmd/server

# Minimal runtime image
FROM scratch
# Or for external dependencies:
FROM alpine:3.19

# Non-root user
RUN adduser -D -u 1000 appuser
USER appuser

COPY --from=builder /app /app
ENTRYPOINT ["/app"]
```

### Docker Compose for Development

```yaml
services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgres://user:pass@db:5432/app
      - LOG_LEVEL=debug
    depends_on:
      db:
        condition: service_healthy
    volumes:
      - ./config:/config:ro

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: app
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d app"]
      interval: 5s
      timeout: 5s
      retries: 5
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

### Temporary File Handling

```go
func ProcessWithTempFiles(ctx context.Context, input []byte) ([]byte, error) {
    // Create temp directory for this operation
    tmpDir, err := os.MkdirTemp("", "process-*")
    if err != nil {
        return nil, fmt.Errorf("create temp dir: %w", err)
    }
    defer os.RemoveAll(tmpDir)  // Clean up

    // Write input to temp file
    inputPath := filepath.Join(tmpDir, "input.pdf")
    if err := os.WriteFile(inputPath, input, 0644); err != nil {
        return nil, fmt.Errorf("write input: %w", err)
    }

    // Process with external tool
    outputPath := filepath.Join(tmpDir, "output.png")
    cmd := exec.CommandContext(ctx, "magick",
        inputPath,
        "-density", "150",
        outputPath,
    )

    if output, err := cmd.CombinedOutput(); err != nil {
        return nil, fmt.Errorf("magick failed: %w\noutput: %s", err, output)
    }

    // Read result
    result, err := os.ReadFile(outputPath)
    if err != nil {
        return nil, fmt.Errorf("read output: %w", err)
    }

    return result, nil
}
```

### Health Check Endpoint

```go
func (h *Handler) Health(w http.ResponseWriter, r *http.Request) {
    checks := map[string]func() error{
        "database":    h.db.Ping,
        "imagemagick": func() error { _, err := exec.LookPath("magick"); return err },
        "redis":       h.redis.Ping,
    }

    results := make(map[string]string)
    healthy := true

    for name, check := range checks {
        if err := check(); err != nil {
            results[name] = fmt.Sprintf("unhealthy: %v", err)
            healthy = false
        } else {
            results[name] = "healthy"
        }
    }

    status := http.StatusOK
    if !healthy {
        status = http.StatusServiceUnavailable
    }

    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(status)
    json.NewEncoder(w).Encode(map[string]any{
        "status": map[bool]string{true: "healthy", false: "unhealthy"}[healthy],
        "checks": results,
    })
}
```

## Anti-Patterns

### Hardcoded Paths

```go
// Bad: Hardcoded path
cmd := exec.Command("/usr/local/bin/magick", args...)
```

```go
// Good: Use PATH lookup
cmd := exec.Command("magick", args...)
```

### Missing Cleanup

```go
// Bad: Temp files accumulate
func Process(input []byte) ([]byte, error) {
    tmpFile, _ := os.CreateTemp("", "input-*")
    os.WriteFile(tmpFile.Name(), input, 0644)
    // No cleanup!
    return result, nil
}
```

```go
// Good: Always clean up
func Process(input []byte) ([]byte, error) {
    tmpFile, _ := os.CreateTemp("", "input-*")
    defer os.Remove(tmpFile.Name())  // Cleanup
    // ...
}
```

### Ignoring Command Output

```go
// Bad: Silently fails
cmd.Run()
```

```go
// Good: Capture and report output
output, err := cmd.CombinedOutput()
if err != nil {
    return fmt.Errorf("command failed: %w\noutput: %s", err, output)
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jaimestill) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
