---
name: go-project-init
description: > Use when this capability is needed.
metadata:
  author: deandum
---

# Go Project Init

Scaffold production-ready Go projects with consistent structure, tooling, and configuration.

## Contents

- [Workflow](#workflow)
- [Project Types](#project-types)
- [Boilerplate Files](#boilerplate-files)
- [Dependencies to Consider](#dependencies-to-consider)
- [Post-Scaffold Checklist](#post-scaffold-checklist)

## Workflow

1. **Clarify project type** — service, CLI, or library
2. **Gather requirements** — name, module path, database, API style (REST/gRPC/both)
3. **Generate structure** — create directories and boilerplate files
4. **Configure tooling** — Makefile, Dockerfile, .golangci.yml, .gitignore
5. **Initialize module** — `go mod init`, add core dependencies
6. **Create entrypoint** — minimal `main.go` with proper wiring

## Project Types

### Service (HTTP/gRPC)

```
myservice/
├── cmd/
│   └── myservice/
│       └── main.go
├── internal/
│   ├── user/                     # Entity-focused package
│   │   ├── user.go               # Domain entity, value objects
│   │   ├── repository.go         # Repository interface (port)
│   │   ├── service.go            # Business logic / use cases
│   │   ├── http.go               # HTTP handlers for user
│   │   ├── grpc.go               # gRPC handlers (if applicable)
│   │   └── postgres.go           # Repository implementation
│   ├── order/                    # Another entity-focused package
│   │   ├── order.go
│   │   ├── service.go
│   │   ├── http.go
│   │   └── postgres.go
│   ├── http/                     # Cross-cutting: shared HTTP concerns
│   │   ├── middleware.go         # Auth, logging, CORS, etc.
│   │   ├── router.go             # Main router setup
│   │   └── errors.go             # Error handling utilities
│   ├── postgres/                 # Cross-cutting: DB infrastructure
│   │   ├── postgres.go           # Connection pooling, health checks
│   │   └── transaction.go        # Transaction utilities
│   ├── shared/                   # Cross-cutting: truly shared utilities
│   │   ├── validator.go          # Input validation
│   │   └── pagination.go         # Common pagination logic
│   └── config/
│       └── config.go             # Configuration from env/files
├── api/
│   └── openapi.yaml              # or proto/ for gRPC
├── migrations/
│   └── 001_init.up.sql
├── scripts/
│   └── dev-setup.sh
├── Makefile
├── Dockerfile
├── docker-compose.yml
├── .golangci.yml
├── .gitignore
├── go.mod
└── go.sum
```

#### Entity-Focused Package Philosophy

Each entity package (e.g., `user/`, `order/`) encapsulates:
- **Domain logic**: Entity types, value objects, validation rules
- **Ports**: Repository and service interfaces
- **Use cases**: Business logic and orchestration
- **Adapters**: HTTP handlers, gRPC handlers, database implementations

**Benefits**:
- High cohesion: related code lives together
- Low coupling: packages depend on each other via interfaces
- Easy navigation: working on "user" features means staying in `internal/user/`
- Natural scaling: adding entities doesn't bloat existing packages

**Cross-cutting packages** handle concerns that span multiple entities:
- `internal/http/`: Middleware, routing, error responses
- `internal/postgres/`: Connection pool, transaction helpers
- `internal/shared/`: Utilities genuinely used across entities (use sparingly)

**Dependency rules**:
- Entity packages can import other entity packages via interfaces
- Entity packages can import cross-cutting packages
- Cross-cutting packages should NOT import entity packages (avoid cycles)

### CLI Tool

```
mytool/
├── cmd/
│   └── mytool/
│       └── main.go
├── internal/
│   ├── command/                  # CLI commands
│   │   ├── root.go
│   │   └── serve.go
│   └── config/
│       └── config.go
├── Makefile
├── .goreleaser.yml
├── .golangci.yml
├── .gitignore
├── go.mod
└── go.sum
```

### Library

```
mylib/
├── mylib.go                     # Main package API
├── mylib_test.go
├── option.go                    # Functional options
├── internal/                    # Private implementation
│   └── parser/
├── examples/
│   └── basic/
│       └── main.go
├── .golangci.yml
├── .gitignore
├── go.mod
└── go.sum
```

## Boilerplate Files

Use the template files in [templates/](templates/) when scaffolding. Replace `{{.Module}}` and `{{.AppName}}` placeholders with actual values.

| File | Template | Description |
|------|----------|-------------|
| `cmd/<name>/main.go` | [main.go.tmpl](templates/main.go.tmpl) | Service entrypoint with graceful shutdown and signal handling |
| `internal/config/config.go` | [config.go.tmpl](templates/config.go.tmpl) | Environment-based configuration using envconfig |
| `Makefile` | [Makefile.tmpl](templates/Makefile.tmpl) | Build, test, lint, migration, and Docker targets |
| `Dockerfile` | [Dockerfile.tmpl](templates/Dockerfile.tmpl) | Multi-stage build with distroless runtime |
| `.gitignore` | [gitignore.tmpl](templates/gitignore.tmpl) | Standard Go project ignores |
| `.golangci.yml` | See the `go-style` skill | Recommended linting configuration |

## Dependencies to Consider

| Need | Recommended | Why |
|------|-------------|-----|
| HTTP router | `net/http` (stdlib) | Good enough for most cases with enhanced routing |
| HTTP router (alternative) | `github.com/go-chi/chi/v5` | Lightweight, idiomatic, composable middleware, compatible with `net/http` |
| Structured logging | `log/slog` (stdlib) | Standard library, zero dependencies |
| Database (Postgres) | `github.com/jackc/pgx/v5` | Best Postgres driver for Go |
| Database (generic SQL) | `database/sql` + driver | Standard interface |
| Database extensions | `github.com/jmoiron/sqlx` | Extends `database/sql` with convenient helpers (Named queries, `Get`, `Select`) |
| Migrations | `github.com/golang-migrate/migrate` | Well-maintained, multiple DB support |
| Configuration | `github.com/kelseyhightower/envconfig` | Declarative config with struct tags, better than Viper for env-only config |
| Validation | `github.com/go-playground/validator/v10` | Powerful struct validation with tags, good for API input validation |
| gRPC | `google.golang.org/grpc` | Official gRPC implementation |
| Testing framework | stdlib `testing` | Table-driven tests are idiomatic |
| Testing framework (BDD) | `github.com/onsi/ginkgo/v2` + `github.com/onsi/gomega` | BDD-style testing with rich matchers, good for integration/e2e tests |
| Mocking | Interface-based manual mocks | Avoid heavy mocking frameworks |
| SQL mocking | `github.com/DATA-DOG/go-sqlmock` | Mock SQL driver for testing database interactions |

**Principle: a little copying is better than a little dependency.** Only add external dependencies when the stdlib cannot do the job, or the dependency genuinely augments it. 

## Post-Scaffold Checklist

- [ ] `go mod tidy` runs cleanly
- [ ] `make lint` passes
- [ ] `make test` passes
- [ ] `make build` produces a binary
- [ ] `docker build` succeeds
- [ ] README.md describes how to run locally
- [ ] `.env.example` documents required environment variables

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deandum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
