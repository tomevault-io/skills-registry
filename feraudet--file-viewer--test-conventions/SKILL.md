---
name: test-conventions
description: Testing standards for this Go project. Use when writing tests, adding test cases, or discussing test coverage. Use when this capability is needed.
metadata:
  author: feraudet
---

# Test Conventions

## Test Structure

Tests should be placed in `main_test.go` alongside `main.go`.

### Table-Driven Tests

```go
func TestSlugify(t *testing.T) {
    tests := []struct {
        name     string
        input    string
        expected string
    }{
        {"simple text", "Hello World", "hello-world"},
        {"with special chars", "Hello! World?", "hello-world"},
        {"with HTML", "<b>Bold</b>", "bold"},
        {"empty", "", "heading-"}, // starts with hash prefix
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := slugify(tt.input)
            if !strings.HasPrefix(result, tt.expected) && result != tt.expected {
                t.Errorf("slugify(%q) = %q, want %q", tt.input, result, tt.expected)
            }
        })
    }
}
```

### HTTP Handler Tests

```go
func TestHandler(t *testing.T) {
    req := httptest.NewRequest("GET", "/test.md", nil)
    w := httptest.NewRecorder()

    handler(w, req)

    if w.Code != http.StatusOK {
        t.Errorf("status = %d, want %d", w.Code, http.StatusOK)
    }

    if !strings.Contains(w.Body.String(), "<!DOCTYPE html>") {
        t.Error("response should contain HTML doctype")
    }
}
```

## Test Commands

```bash
# Run all tests
go test -v

# Run specific test
go test -v -run TestSlugify

# With coverage
go test -cover

# Generate coverage report
go test -coverprofile=coverage.out && go tool cover -html=coverage.out
```

## What to Test

### Priority 1: Core Rendering
- `renderMarkdown()` - All Markdown syntax variants
- `renderJSON()` - Valid/invalid JSON, nested structures
- `slugify()` - Edge cases, special characters

### Priority 2: HTTP Routing
- Path resolution logic
- Query parameter handling
- Asset endpoint security
- Content-Type headers

### Priority 3: Helper Functions
- `replaceEmojis()`
- `processInline()` (if extracted)

## Test File Requirements

When testing file rendering, create temp files:

```go
func TestRenderFile(t *testing.T) {
    tmpDir := t.TempDir()
    testFile := filepath.Join(tmpDir, "test.md")
    os.WriteFile(testFile, []byte("# Hello"), 0644)

    content, class := renderFile(testFile)
    // assertions...
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/feraudet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
