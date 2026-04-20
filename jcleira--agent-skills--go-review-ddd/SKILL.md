---
name: go-review-ddd
description: DDD architecture review for Go projects enforcing layer boundaries, pointer semantics, and domain-driven design patterns. Use when this capability is needed.
metadata:
  author: jcleira
---

# Go DDD Architecture Review

Comprehensive review of Go code for pointer semantics, test coverage, and best practices.

## Note: Opinionated Project Conventions

This skill enforces **opinionated conventions** for DDD-based Go projects:
- **Pointer-first semantics**: Uses `*T` for all model/entity types
- **Specific directory structure**: `internal/app/`, `internal/infra/` layout
- **One-file-per-method**: Service methods in separate files

Standard Go guidance may differ (see `go-style` skill). These are project-specific conventions prioritizing consistency and architectural clarity over micro-optimization.

## 1. Pointer Semantics

Review Go code for correct pointer and value semantics using the "pointer-first" approach.

## Core Principles

1. **Pointer-first for structs**: Use `*T` for all model/entity types by default
2. **Value semantics for primitives**: Keep `string`, `int`, `bool`, etc. as values
3. **Small struct exception**: Structs with 2-3 primitive fields only may use value semantics
4. **Consistency over micro-optimization**: Reducing mental overhead trumps minor performance gains

## Review Checklist

When reviewing Go code, check and flag issues for:

### Function Parameters
- **Issue** if struct parameters use `T` instead of `*T`
- **OK** for primitive types as values
- **OK** for small structs (2-3 primitive fields only)

```go
// Issue: should use pointer
func ProcessUser(user User) { ... }

// Correct
func ProcessUser(user *User) { ... }
```

### Slice Types
- **Issue** if slices of structs use `[]T` instead of `[]*T`

```go
// Issue: should use pointer slice
func GetUsers() []User { ... }

// Correct
func GetUsers() []*User { ... }
```

### Method Receivers
- **Issue** if struct methods use value receivers for anything other than read-only operations on small structs

```go
// Issue: should use pointer receiver
func (u User) UpdateName(name string) { ... }

// Correct
func (u *User) UpdateName(name string) { ... }
```

### Factory Functions
- **Issue** if factory functions return `T` instead of `*T`

```go
// Issue: should return pointer
func NewUser(name string) User { ... }

// Correct
func NewUser(name string) *User { ... }
```

## Output Format

When reviewing, report findings as:

```
**Go Pointer Review**

Issues found:
- `file.go:42` - `ProcessUser(user User)` should use `*User`
- `file.go:58` - `[]User` should be `[]*User`

No issues:
- Factory functions correctly return pointers
- Method receivers use pointer semantics
```

## 2. Test Suite Coverage

When reviewing files in `handlers/`, `services/`, or `repositories/` directories, check for test coverage.

### Review Checklist

1. **Check if `_test.go` file exists** for the file being reviewed
2. **If missing**, flag it and offer to create test scaffolding
3. **If exists**, check for basic coverage of exported functions

### What to Check

- Does a corresponding `_test.go` file exist?
- Are exported functions covered by at least one test?
- Are error cases tested?

### Output Format

```
**Test Coverage Review**

Missing test file:
- `handlers/user.go` has no corresponding `handlers/user_test.go`

Missing test coverage:
- `CreateUser()` has no test coverage
- `DeleteUser()` error path not tested

Offer: Would you like me to create test scaffolding for the missing tests?
```

### When Test Review Triggers

This check runs when reviewing:
- Files in `handlers/` directory
- Files in `services/` directory
- Files in `repositories/` directory

---

## 3. Layer Architecture (DDD)

Verify code follows clean architecture layer boundaries.

### Layer Structure

```
cmd/api/main.go              → Entry point, dependency wiring
internal/app/aggregates/     → Domain entities
internal/app/services/       → Domain services (business logic)
internal/app/usecases/       → Application use cases
internal/infra/http/handlers/→ HTTP handlers (thin, delegate to services)
internal/infra/http/server/  → Router configuration
internal/infra/repositories/ → External service clients
```

### Review Checklist

- **Issue** if business logic appears in handlers (handlers should be thin)
- **Issue** if infrastructure concerns leak into `internal/app/`
- **Issue** if services directly import infrastructure packages
- **OK** for services to define repository interfaces they depend on

```go
// Issue: handler contains business logic
func (h *UserHandler) Create(w http.ResponseWriter, r *http.Request) {
    // Validation, transformation, orchestration here = wrong
}

// Correct: handler delegates to service
func (h *UserHandler) Create(w http.ResponseWriter, r *http.Request) {
    result, err := h.userService.Create(ctx, req)
    // Only HTTP concerns: parse request, write response, log errors
}
```

---

## 4. Service Structure

Services should follow the one-file-per-method pattern.

### Expected Structure

```
services/<name>/
├── service.go           # Service struct, constructor, repository interfaces
├── <method>.go          # One file per public method
└── ...
```

### Review Checklist

- **Issue** if `service.go` contains method implementations beyond constructor
- **Issue** if multiple public methods are in a single file
- **OK** for small helper methods to live alongside their public method

```go
// services/orchestration/service.go - ONLY contains:
type Service struct { ... }
type NotionRepository interface { ... }
func New(...) *Service { ... }

// services/orchestration/process_task.go - Contains:
func (s *Service) ProcessTask(...) { ... }
```

---

## 5. Repository Pattern

Repositories are interfaces defined by the service that uses them, not by the implementation.

### Review Checklist

- **Issue** if repository interface is defined in `internal/infra/repositories/`
- **Issue** if service imports concrete repository types
- **OK** for interface to be defined in the service's `service.go`

```go
// Correct: interface in service.go
// services/llm/service.go
type Repository interface {
    Process(ctx context.Context, request *Request) (*Response, error)
    HealthCheck(ctx context.Context) error
}

// Issue: interface in repository package
// infra/repositories/claude/repository.go
type Repository interface { ... }  // Wrong location!
```

---

## 6. Error Handling

Errors should be wrapped with context and logged at appropriate levels.

### Review Checklist

- **Issue** if errors are returned without context wrapping
- **Issue** if errors are logged in services (should log at handler level)
- **Issue** if using `log.Println` instead of structured logging
- **OK** for `fmt.Errorf("failed to X: %w", err)` pattern

```go
// Issue: no context
return err

// Issue: logging in service
func (s *Service) Process() error {
    log.Println("error:", err)  // Wrong!
    return err
}

// Correct: wrap with context
return fmt.Errorf("failed to process task %s: %w", taskID, err)

// Correct: log at handler level with structured logging
logger.ErrorContext(ctx, "failed to process request", "error", err, "task_id", taskID)
```

---

## 7. Naming Conventions

Follow consistent naming patterns for types and files.

### Patterns

| Type | File Name | Type Name |
|------|-----------|-----------|
| Service | `<domain>/service.go` | `type Service struct` |
| Repository | `<provider>/repository.go` | `type Repository struct` |
| Handler | `<name>_handler.go` | `type <Name>Handler struct` |

### Review Checklist

- **Issue** if service type is not named `Service`
- **Issue** if repository type is not named `Repository`
- **Issue** if handler files don't end in `_handler.go`
- **Issue** if handler type doesn't match `<Name>Handler`

```go
// Issue
type UserService struct { ... }      // Should be: type Service struct
type ClaudeRepository struct { ... } // Should be: type Repository struct
type WebhookController struct { ... }// Should be: type WebhookHandler struct

// Correct
type Service struct { ... }    // in services/user/service.go
type Repository struct { ... } // in repositories/claude/repository.go
type WebhookHandler struct { ... } // in handlers/webhook_handler.go
```

---

## When to Run

This skill auto-invokes after writing or modifying Go files. It checks:
- Pointer semantics for struct definitions, functions, methods, and return types
- Test coverage for handlers, services, and repositories
- Layer boundaries and DDD compliance
- Service structure (one file per method)
- Repository pattern (interfaces in consumers)
- Error handling patterns
- Naming conventions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcleira) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
