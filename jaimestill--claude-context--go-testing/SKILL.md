---
name: go-testing
description: > Use when this capability is needed.
metadata:
  author: jaimestill
---

# Go Testing

## When This Skill Applies

- Writing or editing test files (*_test.go)
- Discussing test coverage
- Designing test strategies
- Setting up test infrastructure

## Principles

### 1. Test Organization Structure

Tests organized in separate `tests/` directory mirroring `internal/` structure.

```
project/
├── internal/
│   ├── config/
│   │   └── agent.go
│   └── agent/
│       └── agent.go
└── tests/
    ├── config/
    │   └── agent_test.go
    └── agent/
        └── agent_test.go
```

**Benefits**:
- Production code directories stay clean
- Mirror structure makes files easy to locate
- Clear separation of concerns

### 2. Black-Box Testing Approach

All tests use `package <name>_test`, testing only the public API.

```go
package config_test

import (
    "testing"
    "github.com/user/project/internal/config"
)

func TestAgentConfig_Validate(t *testing.T) {
    cfg := config.AgentConfig{Name: "test"}
    if err := cfg.Validate(); err != nil {
        t.Errorf("unexpected error: %v", err)
    }
}
```

**Benefits**:
- Tests validate from consumer perspective
- Prevents tests depending on implementation details
- Refactoring safer without breaking tests
- Reduces test volume

### 3. Table-Driven Test Pattern

Use table-driven tests for parameterized testing scenarios.

```go
func TestExtractOption(t *testing.T) {
    tests := []struct {
        name         string
        options      map[string]any
        key          string
        defaultValue float64
        expected     float64
    }{
        {
            name:         "key exists with correct type",
            options:      map[string]any{"temperature": 0.7},
            key:          "temperature",
            defaultValue: 0.5,
            expected:     0.7,
        },
        {
            name:         "key missing returns default",
            options:      map[string]any{},
            key:          "temperature",
            defaultValue: 0.5,
            expected:     0.5,
        },
        {
            name:         "wrong type returns default",
            options:      map[string]any{"temperature": "hot"},
            key:          "temperature",
            defaultValue: 0.5,
            expected:     0.5,
        },
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := config.ExtractOption(tt.options, tt.key, tt.defaultValue)
            if result != tt.expected {
                t.Errorf("got %v, want %v", result, tt.expected)
            }
        })
    }
}
```

### 4. HTTP Mocking for Integration Tests

Use `httptest.Server` for provider testing without live services.

```go
func TestProvider_Request(t *testing.T) {
    server := httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        // Verify request
        if r.Method != http.MethodPost {
            t.Errorf("expected POST, got %s", r.Method)
        }
        if r.Header.Get("Content-Type") != "application/json" {
            t.Errorf("expected JSON content type")
        }

        // Return mock response
        w.Header().Set("Content-Type", "application/json")
        json.NewEncoder(w).Encode(map[string]string{
            "status": "ok",
        })
    }))
    defer server.Close()

    // Use server.URL to configure provider
    provider := NewProvider(WithEndpoint(server.URL))
    result, err := provider.Request(context.Background(), req)
    if err != nil {
        t.Fatalf("unexpected error: %v", err)
    }
    // Assert on result
}
```

### 5. Test Coverage Success Criteria

Coverage measured by critical path validation, not arbitrary percentages.

**Must Cover (100%)**:
- Happy paths (normal operation flows)
- Security paths (input validation, injection prevention)
- Error types (domain errors are distinguishable)
- Integration points (lifecycle hooks, system boundaries)

**Acceptable Gaps**:
- Defensive error handling for OS-level failures
- Edge cases requiring filesystem mocking
- Error wrapping paths that don't affect behavior

**Minimum Requirement**: 80% overall coverage

### 6. Test Naming Conventions

Format: `Test<Type>_<Method>_<Scenario>`

```go
// Testing a type's method
func TestDuration_UnmarshalJSON_ParsesStringFormat(t *testing.T)
func TestDuration_UnmarshalJSON_RejectsInvalidFormat(t *testing.T)

// Testing a function
func TestNewAgent_ValidConfig(t *testing.T)
func TestNewAgent_MissingName(t *testing.T)

// Testing behavior
func TestAgent_Execute_ReturnsResult(t *testing.T)
func TestAgent_Execute_HandlesTimeout(t *testing.T)
```

### 7. Skip Gracefully for Missing Dependencies

Tests requiring external binaries skip gracefully when dependencies are missing.

```go
func TestWithImageMagick(t *testing.T) {
    if _, err := exec.LookPath("magick"); err != nil {
        t.Skip("ImageMagick not installed")
    }

    // Test implementation that requires ImageMagick
    result, err := processImage(input)
    if err != nil {
        t.Fatalf("unexpected error: %v", err)
    }
    // Assertions
}
```

## Patterns

### Test Helper Functions

```go
// Helper for common setup
func setupTestDB(t *testing.T) *sql.DB {
    t.Helper()
    db, err := sql.Open("sqlite3", ":memory:")
    if err != nil {
        t.Fatalf("failed to open db: %v", err)
    }
    t.Cleanup(func() { db.Close() })
    return db
}

// Usage
func TestRepository_Find(t *testing.T) {
    db := setupTestDB(t)
    repo := NewRepository(db)
    // Test
}
```

### Assertion Helpers

```go
func assertEqual[T comparable](t *testing.T, got, want T) {
    t.Helper()
    if got != want {
        t.Errorf("got %v, want %v", got, want)
    }
}

func assertNoError(t *testing.T, err error) {
    t.Helper()
    if err != nil {
        t.Fatalf("unexpected error: %v", err)
    }
}
```

### Context with Timeout

```go
func TestLongOperation(t *testing.T) {
    ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
    defer cancel()

    result, err := longOperation(ctx)
    if err != nil {
        t.Fatalf("operation failed: %v", err)
    }
    // Assertions
}
```

## Anti-Patterns

### Testing Implementation Details

```go
// Bad: Tests internal state
func TestCache_InternalMap(t *testing.T) {
    c := &Cache{}
    c.items["key"] = "value"  // Accessing private field
}
```

```go
// Good: Tests public behavior
func TestCache_GetAfterSet(t *testing.T) {
    c := NewCache()
    c.Set("key", "value")
    got := c.Get("key")
    assertEqual(t, got, "value")
}
```

### Flaky Time-Based Tests

```go
// Bad: Depends on real time
func TestTimeout(t *testing.T) {
    start := time.Now()
    doSomething()
    if time.Since(start) > time.Second {
        t.Error("too slow")
    }
}
```

```go
// Good: Use context or mock clock
func TestTimeout(t *testing.T) {
    ctx, cancel := context.WithTimeout(context.Background(), time.Second)
    defer cancel()

    err := doSomethingWithContext(ctx)
    if !errors.Is(err, context.DeadlineExceeded) {
        t.Errorf("expected timeout, got %v", err)
    }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jaimestill) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
