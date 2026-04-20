---
name: go-dev-guidelines
description: This skill should be used when writing, refactoring, or testing Go code. It provides idiomatic Go development patterns, TDD-based workflows, project structure conventions, and testing best practices using testify/require and mockery. Activate this skill when creating new Go features, services, packages, tests, or when setting up new Go projects. Use when this capability is needed.
metadata:
  author: jumppad-labs
---

# Go Development Guidelines

## Overview

This skill provides comprehensive guidelines for idiomatic Go development with a Test-Driven Development (TDD) approach. Follow these patterns when writing Go code, creating tests, organizing projects, or refactoring existing code.

## Quick Start Checklists

### New Go Feature Checklist

When implementing a new feature in an existing Go project:

1. **Define interface** - Create small, focused interface in appropriate package
2. **Write tests first** - Create `*_test.go` file with testify/require tests
3. **Generate mocks** - Use mockery to generate mocks in `mocks/` subfolder
4. **Implement logic** - Write the implementation to satisfy tests
5. **Handle errors** - Ensure all errors are explicitly handled
6. **Add integration tests** - Test the feature end-to-end if applicable
7. **Run go vet & gofmt** - Ensure code meets Go standards
8. **Update documentation** - Add godoc comments for exported types/functions

### New Go Service/Package Checklist

When creating a new Go service or package from scratch:

1. **Setup project structure** - Use standard Go layout (`/cmd`, `/internal`, `/pkg`)
2. **Initialize module** - Run `go mod init` with appropriate module path
3. **Define core interfaces** - Start with small, focused interfaces
4. **Write tests first** - Follow TDD approach for all business logic
5. **Implement with DI** - Use dependency injection for testability
6. **Add logging** - Include structured logging for observability
7. **Configure graceful shutdown** - Implement proper cleanup for services
8. **Document package** - Add package-level godoc and README

## Core Principles

Follow these seven core principles for all Go development:

### 1. Follow Test-Driven Development (TDD)

Write tests before implementation. Tests should be easy to read and favor verbosity over abstraction.

### 2. Use testify/require for Unit Tests

All unit tests must use `github.com/stretchr/testify/require` for assertions.

### 3. Use Mockery for Mocks

Generate mocks using mockery. Mocks must be localized in a `mocks/` subfolder next to the interface being mocked.

### 4. Never Use Table-Driven Tests

Avoid table-driven tests. Write explicit test functions for each scenario.

### 5. Never Mix Positive and Negative Tests

Keep positive (success) and negative (error) test cases in separate test functions.

### 6. Handle All Errors Explicitly

Never ignore errors. Always handle them explicitly or return them to the caller.

### 7. Prefer Small, Focused Interfaces

Design interfaces with few methods. Use composition over large interfaces.

### 8. Use `any` Instead of `interface{}`

For generic types, prefer `any` over `interface{}` (Go 1.18+).

## Standard Go Directory Structure

```
project-root/
├── cmd/                  # Main applications
│   └── myapp/
│       └── main.go
├── internal/             # Private application code
│   ├── handler/          # HTTP handlers
│   │   ├── handler.go
│   │   ├── handler_test.go
│   │   └── mocks/        # Mocks for handler interfaces
│   ├── service/          # Business logic
│   │   ├── service.go
│   │   ├── service_test.go
│   │   └── mocks/
│   └── repository/       # Data access
│       ├── repository.go
│       ├── repository_test.go
│       └── mocks/
├── pkg/                  # Public library code
│   └── client/
│       ├── client.go
│       ├── client_test.go
│       └── mocks/
├── api/                  # API definitions (OpenAPI, protobuf)
├── configs/              # Configuration files
├── go.mod
├── go.sum
└── README.md
```

## Quick Reference

### Common Test Patterns

```go
// Unit test with mock
func TestServiceCreate(t *testing.T) {
    mockRepo := mocks.NewRepository(t)
    mockRepo.On("Save", mock.Anything).Return(nil)

    svc := NewService(mockRepo)
    err := svc.Create(context.Background(), data)

    require.NoError(t, err)
    mockRepo.AssertExpectations(t)
}

// Separate negative test
func TestServiceCreate_RepoError(t *testing.T) {
    mockRepo := mocks.NewRepository(t)
    mockRepo.On("Save", mock.Anything).Return(errors.New("db error"))

    svc := NewService(mockRepo)
    err := svc.Create(context.Background(), data)

    require.Error(t, err)
    require.Contains(t, err.Error(), "db error")
}
```

### Naming Conventions

- **Packages**: Short, lowercase, no underscores (`handler`, `service`)
- **Files**: Lowercase with underscores (`user_service.go`, `user_service_test.go`)
- **Types**: PascalCase (`UserService`, `HTTPHandler`)
- **Functions/Methods**: PascalCase for exported, camelCase for unexported
- **Interfaces**: Often end with `-er` suffix (`Reader`, `Writer`, `UserRepository`)

## Navigation Table

Use this table to find detailed guidance for specific tasks:

| If You Need To... | See This Resource |
|-------------------|-------------------|
| Set up a new Go project structure | [Project Structure](references/project-structure.md) |
| Understand Go naming conventions | [Naming Conventions](references/naming-conventions.md) |
| Write tests with TDD, testify/require, and mockery | [Testing Guide](references/testing-guide.md) |
| Organize packages, interfaces, and dependencies | [Code Organization](references/code-organization.md) |
| Handle errors idiomatically | [Error Handling](references/error-handling.md) |
| Work with goroutines, channels, and context | [Concurrency Patterns](references/concurrency-patterns.md) |
| Manage dependencies and go.mod | [Dependencies](references/dependencies.md) |
| See complete working examples | [Complete Examples](references/complete-examples.md) |

## Resources

This skill includes detailed reference documentation in the `references/` directory. Claude will load these resources as needed when working on specific tasks.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jumppad-labs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
