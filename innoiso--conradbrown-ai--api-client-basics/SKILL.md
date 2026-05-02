---
name: api-client-basics
description: Use the api-client tool to call HTTP APIs with path-only endpoints, headers, and JSON bodies (e.g., GET /v2/stories, POST /account/login). Use when this capability is needed.
metadata:
  author: innoiso
---

# API Client Basics

Use this skill whenever you need to hit HTTP endpoints via the `api-client` tool.

## Setup
- If `API_BASE_URL` is configured, pass only the path (e.g., `/v2/stories`, `/account/login`).
- DO NOT USE A FULL URL. DO NOT INCLUDE http://localhost. If the base url is not set, STOP and alert the user
- Set common headers with `set_header` (e.g., `Authorization: Bearer <token>`, `Content-Type: application/json` for JSON POST/PUT/PATCH).
- Remove headers with `remove_header` if switching auth contexts.

## Calling Patterns
- GET example (list stories):
  ```json
  http_get { "path": "/v2/stories" }
  ```
- POST example (login):
  ```json
  http_post {
    "path": "/account/login",
    "headers": { "Content-Type": "application/json" },
    "body": { "email": "user@example.com", "password": "password" }
  }
  ```
- PUT/PATCH example (update resource):
  ```json
  http_put {
    "path": "/v2/stories/123",
    "headers": { "Content-Type": "application/json" },
    "body": { "name": "Updated title" }
  }
  ```
- DELETE example:
  ```json
  http_delete { "path": "/v2/stories/123" }
  ```

## Tips
- Always include `Content-Type: application/json` when sending JSON bodies.
- Reuse auth headers across calls using `set_header`; clear them when done if you must avoid leakage.
- Keep bodies minimal; only send required fields.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/innoiso) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
