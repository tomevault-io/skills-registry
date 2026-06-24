---
name: code-review
description: Standards for reviewing Go code changes in this project. Use when asked to review code, check changes, or validate pull requests. Use when this capability is needed.
metadata:
  author: feraudet
---

# Code Review Standards

## Checklist

### Security
- [ ] Path traversal: All file paths use `filepath.Clean()` before access
- [ ] XSS prevention: User content escaped with `html.EscapeString()`
- [ ] No arbitrary command execution
- [ ] HTTP headers set correctly (Content-Type)

### Go Idioms
- [ ] Errors handled immediately after function calls
- [ ] `defer` used for cleanup (Close, etc.)
- [ ] No shadowed variables
- [ ] Consistent naming (camelCase for private, PascalCase for exported)

### HTTP Handler Quality
- [ ] Request logging present
- [ ] Appropriate status codes returned
- [ ] Content-Type header set
- [ ] Early returns for error cases

### Markdown Parser
- [ ] New features don't break existing syntax
- [ ] HTML properly escaped in output
- [ ] Inline processing order preserved (math/code first)
- [ ] State machines properly closed (lists, tables, code blocks)

### Performance
- [ ] No regex compilation inside hot loops
- [ ] `strings.Builder` used for string concatenation
- [ ] HTTP client has timeout set

## Common Issues

### Path Security
```go
// BAD: Direct path usage
http.ServeFile(w, r, r.URL.Query().Get("path"))

// GOOD: Clean and validate
path := filepath.Clean(r.URL.Query().Get("path"))
info, err := os.Stat(path)
if err != nil || info.IsDir() {
    http.Error(w, "Not found", 404)
    return
}
http.ServeFile(w, r, path)
```

### HTML Escaping
```go
// BAD: Raw user content in HTML
fmt.Sprintf("<p>%s</p>", userContent)

// GOOD: Escaped content
fmt.Sprintf("<p>%s</p>", html.EscapeString(userContent))
```

## Review Commands

```bash
# Check for unescaped HTML output
grep -n 'fmt.Sprintf.*<' main.go | grep -v 'EscapeString'

# Find path usage without Clean
grep -n 'os.Stat\|os.ReadFile\|http.ServeFile' main.go
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/feraudet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
