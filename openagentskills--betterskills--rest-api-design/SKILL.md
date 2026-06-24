---
name: rest-api-design
description: >- Use when this capability is needed.
metadata:
  author: openagentskills
---

# REST API Design

## Resource naming

- Nouns, plural collections: `/users`, `/users/{id}/orders`
- Actions as sub-resources when not CRUD: `/users/{id}/activate` (prefer state change via PATCH)
- Kebab-case paths; lowercase

## HTTP methods

| Method | Idempotent | Safe | Typical use |
|--------|------------|------|-------------|
| GET | Yes | Yes | Read |
| POST | No | No | Create, non-idempotent actions |
| PUT | Yes | No | Full replace |
| PATCH | No* | No | Partial update |
| DELETE | Yes | No | Remove |

## Status codes (defaults)

| Code | When |
|------|------|
| 200 | Success with body |
| 201 | Created (+ `Location` header) |
| 204 | Success, no body |
| 400 | Client validation error |
| 401 | Not authenticated |
| 403 | Authenticated, not allowed |
| 404 | Resource not found |
| 409 | Conflict (duplicate, state) |
| 422 | Semantic validation (optional) |
| 429 | Rate limited |
| 500 | Server error (no internal details) |

## Error body (consistent shape)

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable summary",
    "details": [{ "field": "email", "issue": "invalid format" }]
  }
}
```

## Pagination

Prefer cursor-based for large sets:

```json
{
  "data": [],
  "next_cursor": "opaque-token",
  "has_more": true
}
```

Offset acceptable for admin or small tables; document limits (`limit` max 100).

## Versioning

- URL prefix (`/v1/`) or header (`Accept: application/vnd.company.v1+json`)
- Never break clients without a new version or deprecation window

## Review checklist

- [ ] Consistent naming and pluralization
- [ ] Correct status codes (not 200 for errors)
- [ ] Filtering/sorting documented
- [ ] Auth on every mutating route
- [ ] Idempotency keys for POST payments/bookings where needed

---
> Source: [openagentskills/betterskills](https://github.com/openagentskills/betterskills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
