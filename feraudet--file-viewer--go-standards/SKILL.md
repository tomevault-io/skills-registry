---
name: go-standards
description: Go coding standards and idioms for this project. Use when writing or reviewing Go code, adding new handlers, or modifying the Markdown parser. Use when this capability is needed.
metadata:
  author: feraudet
---

# Go Standards

## Project Conventions

This is a single-file Go HTTP server. All code lives in `main.go`.

### Error Handling

- Return early on errors with descriptive messages
- Use `fmt.Errorf` for wrapped errors with context
- HTTP handlers: use `http.Error()` for client errors, log server errors

```go
// Good
if err != nil {
    return fmt.Errorf("failed to read file %s: %w", path, err)
}

// Bad
if err != nil {
    return err
}
```

### HTTP Handlers

- Log all requests with timestamp: `fmt.Printf("[%s] %s %s\n", time.Now().Format("15:04:05"), r.Method, r.URL.String())`
- Clean paths with `filepath.Clean()` before use
- Set appropriate `Content-Type` headers
- Return early for special endpoints (asset, mtime)

### String Building

- Use `strings.Builder` for building HTML/text output
- Use `fmt.Sprintf` for simple formatting
- Escape user content with `html.EscapeString()`

### Regex Patterns

- Compile regexes once at package level for reuse, or inline for clarity in small functions
- Use `FindStringSubmatch` to extract groups
- Use `ReplaceAllStringFunc` for complex replacements

### Code Organization

Functions should follow this order:
1. Package-level variables (constants, maps, structs)
2. Helper functions (slugify, replaceEmojis, etc.)
3. `main()`
4. HTTP handler
5. Render functions by type (renderFile, renderMarkdown, renderJSON, etc.)
6. HTML template builder

### Adding New File Format Support

1. Add case in `renderFile()` switch statement
2. Create `renderXXX(content string) string` function
3. Add CSS styles in `buildHTML()` if needed
4. Add format to CLAUDE.md supported formats table

### Adding New Markdown Features

Location: `renderMarkdown()` function (~350 lines)

1. Block elements: Add regex check in main loop before paragraph fallback
2. Inline elements: Add to `processInline()` closure
3. Preserve order: math/code first (protect from other processing)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/feraudet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
