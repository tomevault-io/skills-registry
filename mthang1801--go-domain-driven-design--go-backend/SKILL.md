---
name: go-backend-development
description: Patterns for building Go HTTP Backends using Gin, Wire, and standard library Use when this capability is needed.
metadata:
  author: mthang1801
---

## Patterns
- Gin handler patterns
- Middleware (auth, logging, rate limit)
- Google Wire DI
- Error handling with domain errors
- Repository pattern
- Testing with mocks

## Example

```go
// ✅ GOOD: Thin handler, delegate to use case
func (h *Handler) CreateUser(c *gin.Context) {
    user, err := h.useCase.Execute(ctx, cmd)
    if err != nil {
        h.handleError(c, err)
        return
    }
    c.JSON(201, toResponse(user))
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mthang1801) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
