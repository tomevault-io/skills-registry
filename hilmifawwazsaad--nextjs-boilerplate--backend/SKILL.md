---
name: backend
description: Design and generate external backend API code (any language/framework: Go, Rust, Python, PHP, JavaScript, Ruby, etc.) that serves a Next.js TSX frontend. Use for API endpoints, auth, validation, and data modeling on a separate backend server. Use when this capability is needed.
metadata:
  author: hilmifawwazsaad
---

## Pre-Code Checklist

1. Resource + HTTP method + action
2. Request → response contract (input shape, output shape, errors)
3. Auth requirement (public / authenticated / role-gated)
4. Failure scenarios + validation rules

## Response Envelope

```json
{ "success": true,  "data": "<payload>", "message": "OK",        "error": null }
{ "success": false, "data": null,         "message": "<summary>", "error": { "code": "<CODE>", "details": [{ "field": "", "message": "" }] } }
```

## HTTP Status Codes

| Code | When                                             |
| ---- | ------------------------------------------------ |
| 200  | GET · PUT · PATCH success                        |
| 201  | POST created                                     |
| 204  | DELETE success (no body)                         |
| 400  | Malformed request                                |
| 401  | Missing / invalid auth                           |
| 403  | Authenticated but forbidden                      |
| 404  | Resource not found                               |
| 409  | Conflict / duplicate                             |
| 422  | Validation failure + `error.details` array       |
| 429  | Rate limited                                     |
| 500  | Server error — safe message only, no stack trace |

## Design Rules

- **Versioning** — prefix all routes `/api/v1/`.
- **Endpoints** — plural nouns, kebab-case: `/api/v1/posts`, `/api/v1/post-categories`.
- **Query params** — `page`, `limit`, `sort`, `filter[key]=value`.
- **Auth** — `Authorization: Bearer <token>` (JWT/opaque) or HTTP-only cookie (same parent domain).
- **Pagination** — all list endpoints: `data: { items: T[], pagination: { page, limit, totalItems, totalPages } }`.
- **CORS** — exact origin only (`http://localhost:3000` dev, exact domain prod). Never `*` with credentials.
- **Naming** — pick camelCase or snake_case; consistent throughout the project.
- **Docs** — expose OpenAPI spec at `/api/docs` or `/openapi.json` when feasible.

## Architecture

- Route handlers thin — business logic in service/use-case layer.
- Global error handler → catches all unhandled errors → safe 500.
- DB connection pooling. Async/non-blocking I/O.
- Cache read-heavy data (Redis or in-memory).

## Security (non-negotiable)

- Secrets via env vars only — never hardcoded.
- Parameterized queries/ORM — no string-concatenated SQL.
- Hash passwords (bcrypt/argon2 or language equivalent).
- Rate-limit auth endpoints (login, register, password reset).
- Limit request body size (e.g., 1 MB).
- Security headers in production (X-Frame-Options, CSP, HSTS).
- Validate all input at boundary before processing.

## Never Do

- Expose stack traces, DB errors, or internal details to client.
- Trust unvalidated client input or hardcode secrets.
- Skip pagination on list endpoints.
- Wildcard CORS `*` with credentials.
- Business logic directly in route handlers.

---
> Source: [hilmifawwazsaad/NextJS-Boilerplate](https://github.com/hilmifawwazsaad/NextJS-Boilerplate) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
