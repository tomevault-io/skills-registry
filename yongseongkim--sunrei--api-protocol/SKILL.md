---
name: api-protocol
description: API protocol guide for Sunrei project. Use when writing API endpoints, request/response type naming, and HTTP method conventions. Use when this capability is needed.
metadata:
  author: yongseongkim
---

# API Protocol Guide

RESTful API + OpenAPI 3.0 spec

- Request type: `{HttpMethod}{Name}Params` (e.g., `GetUserParams`, `ListUsersParams`)
- Response type: `{HttpMethod}{Name}Result` (e.g., `GetUserResult`, `ListUsersResult`)

## HTTP Method Rules

- `GET` + singular → single item (e.g., `GET /sunreis/{id}`)
- `List` + plural → list items (e.g., `GET /sunreis`)
- `POST` → create
- `PUT` → full update
- `PATCH` → partial update
- `DELETE` → delete

## Response Format

- Success: `{ "data": ... }` or domain key (e.g., `{ "sunreis": [...] }`)
- Error: `{ "error": { "code": "...", "message": "..." } }`
- Pagination: `{ "items": [...], "nextToken": "..." }`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yongseongkim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
