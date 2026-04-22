---
name: middleware-skill
description: Use this skill to implement HTTP middleware pattern in go-zero projects. It provides templates for creating custom middleware and guides on how to register them in .api files or global routes.
metadata:
  author: penitence1992
---

# Middleware Implementation

This skill provides standard patterns for implementing and registering HTTP middleware in go-zero.

## Execution Steps

### Step 1: Create Middleware
Create a new file in `infra/middleware/` (or `internal/middleware/`) using the template.

**Template Usage:**
- Source: `templates/middleware.go.tmpl`
- Target: `infra/middleware/{{NAME_LOWER}}_middleware.go`
- **Variables:**
  - `{{MIDDLEWARE_NAME}}`: Middleware name (e.g., `Auth`, `Log`)

### Step 2: Register in API Definition (Optional)
If the middleware is for specific routes, register it in the `.api` file.

```go
@server(
    middleware: {{MIDDLEWARE_NAME}}Middleware
)
service your-api {
    @handler SomeHandler
    get /some/path
}
```

### Step 3: Configure in ServiceContext (Global)
If the middleware is global or used via ServiceContext, initialize it in `internal/svc/service_context.go` and add it to `routes.go` logic (if manual) or use the `.api` registration above (recommended).

If using global middleware for all routes (not via .api):
In `main.go`:
```go
server.Use(middleware.New{{MIDDLEWARE_NAME}}Middleware().Handle)
```

## Templates Location
- `templates/middleware.go.tmpl`: Standard Middleware struct and Handle function.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/penitence1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
