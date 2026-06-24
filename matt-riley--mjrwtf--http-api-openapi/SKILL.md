---
name: http-api-openapi
description: Keep HTTP handlers and OpenAPI (openapi.yaml) in sync. Use when adding/changing endpoints, request/response schemas, auth requirements, or error shapes. Use when this capability is needed.
metadata:
  author: matt-riley
---

## Tooling assumptions

- Use a terminal runner with bash and git available.
- Prefer `make` targets when available; fall back to direct CLI commands when needed.

## Source of truth

- OpenAPI spec: `openapi.yaml` at the repo root.

## Typical workflow

1. Update `openapi.yaml` (paths, schemas, auth).
2. Validate the spec:

```bash
make validate-openapi
```

If `swagger-cli` isn’t installed:

```bash
npm install -g @apidevtools/swagger-cli
```

3. Implement the handler changes in Go (and keep auth consistent with the spec).
4. Run tests:

```bash
make test
```

## Project-specific notes

- Authenticated endpoints use Bearer token auth (see README’s Auth section).
- Be explicit about error responses and status codes in the spec when behavior changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matt-riley) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
