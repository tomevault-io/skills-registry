---
name: adapter-web-controller-generator
description: Use when generating web adapters (controllers/DTOs/assemblers) that map HTTP requests to app use-cases without business logic leakage.
metadata:
  author: ryan-alexander-zhang
---

# Adapter Web Controller Generator

## Overview
Generates adapter-layer web controllers, DTOs, and assemblers that translate HTTP into app-layer commands and queries. Controllers must be thin — no domain rules.

**REQUIRED:** Follow `GENERATOR_SKILL_STRUCTURE.md`. Variables in `VARIABLES.md`. Templates in `references/templates.md`.

## When to Use
- Generating or modifying controllers, DTOs, and assemblers under `{{basePackage}}.adapter.web.*`.

### Don't use when
- The endpoint is an internal MQ consumer (use `adapter-mq-consumer-generator`) or a scheduled job (use `adapter-scheduler-job-generator`). Not for domain model changes.

## Inputs Required
- Endpoint path + method
- Request/response schema
- Called app use-case (command/query)

## Outputs
- `.../adapter/web/{{bcName}}/controller/<XxxController>.java`
- DTOs under `.../adapter/web/{{bcName}}/dto`
- Assembler under `.../adapter/web/{{bcName}}/assembler` when mapping is non-trivial
- `.../adapter/web/common/GlobalExceptionHandler.java` (generated once, not per controller)
- `.../adapter/web/common/ErrorResponse.java` (generated once, not per controller)

## Naming & Packaging
- Follow BC-first `adapter.web.{{bcName}}.*` packages
- Shared only in `adapter.web.common`

## Rules
- Controllers validate/parse inputs and call app handlers/services.
- No domain rules implemented here.
- DTOs should be adapter-specific (do not reuse domain objects as request/response).
- Invalid query parameters must return HTTP `400` with explicit error code/message; do not silently coerce malformed values.
- Generate `GlobalExceptionHandler` and `ErrorResponse` in `adapter/web/common/` if they don't already exist. This is a one-time generation per project.

## Reference Implementations
- `{{adapterModuleDir}}/src/main/java/{{basePackagePath}}/adapter/web/{{bcName}}/controller/package-info.java`
- `{{adapterModuleDir}}/src/main/java/{{basePackagePath}}/adapter/web/common/package-info.java`

## Tests
- Unit tests should validate request mapping and parameter validation when non-trivial.
- Minimum verification: `mvn -q clean test`.

## Common Mistakes
| Mistake | Why It Happens | Fix |
|---------|---------------|-----|
| Exposing infra POs in web responses | Devs reuse persistence objects for convenience | Create adapter-specific DTOs instead |
| Implementing domain validation in controllers | Tempting to add checks close to the request | Move validation to domain/app layer |
| Reusing domain objects as request/response DTOs | Seems like less code to write | Creates coupling — use adapter-specific DTOs |

## Integration
- **Called by:** `scaffold-router`
- **Pairs with:** `app-usecase-generator`

## Commit Gate

- pass required tests (`mvn -q clean test` minimum, `mvn -q clean verify` if DB behavior changed)
- run `requesting-code-review`
- resolve Critical/Important findings
- keep the commit focused

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryan-alexander-zhang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
