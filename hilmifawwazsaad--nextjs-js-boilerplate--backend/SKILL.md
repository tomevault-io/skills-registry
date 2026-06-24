---
name: backend
description: External backend API (any language: Go, Rust, Python, PHP, JS, etc.) serving a Next.js JSX frontend. Use when this capability is needed.
metadata:
  author: hilmifawwazsaad
---

## Before Writing

Identify: resource/action, request→response contract, auth requirement, failure scenarios, validation needs.

## Response Envelope

- Success: `{ success: true, data: <payload>, message: "OK", error: null }`
- Error: `{ success: false, data: null, message: "<summary>", error: { code: "<CODE>", details: [{field, message}] } }`

## HTTP Codes

| Situation          | Code                      |
| ------------------ | ------------------------- |
| GET/PUT/PATCH      | 200                       |
| POST created       | 201                       |
| DELETE             | 204                       |
| Validation failure | 422 + field-level details |
| Invalid auth       | 401                       |
| Forbidden          | 403                       |
| Conflict           | 409                       |
| Rate limited       | 429                       |
| Server error       | 500 (no stack trace)      |

## Design Rules

- **Endpoints** — plural nouns, kebab-case: `/posts`, `/post-categories`. Query: `page`, `limit`, `sort`, `filter[key]=value`.
- **Auth** — JWT/opaque token via `Authorization: Bearer <token>`, or HTTP-only cookie if same domain.
- **Pagination** — lists return `{ items, pagination: { page, limit, totalItems, totalPages } }` in `data`.
- **CORS** — exact origin only. Never `*` with credentials.
- **Validation** — validate all input at boundary; 422 + `error.details` array.
- **Naming** — camelCase or snake_case; consistent throughout.
- **Docs** — OpenAPI at `/api/docs` or `/openapi.json` when feasible.

## Security

- Secrets in env vars only, never hardcoded.
- Parameterized queries/ORM. No string concatenation for DB.
- Hash passwords (bcrypt/argon2).
- Rate limit auth endpoints (login, register, password reset).
- Limit request body size (1MB).
- Security headers (X-Frame-Options, CSP) in production.
- HTTPS in production.

## Architecture

- Thin route handlers — business logic in service/use-case layer.
- Global error handler → safe 500 for unexpected errors.
- DB connection pooling. Async I/O.
- Cache read-heavy data where appropriate.

## Never Do

- Expose stack traces, DB errors, internals to client.
- Trust unvalidated input or hardcode secrets.
- Skip pagination on list endpoints.
- `*` CORS with credentials.
- Business logic in route handlers.

---
> Source: [hilmifawwazsaad/NextJS-js-Boilerplate](https://github.com/hilmifawwazsaad/NextJS-js-Boilerplate) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
