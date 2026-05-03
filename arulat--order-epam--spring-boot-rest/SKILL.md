---
name: spring-boot-rest
description: Patterns and conventions for Spring Boot REST APIs (controllers, DTOs, validation, error handling). Use when this capability is needed.
metadata:
  author: arulat
---

## When to use
Use this skill when implementing endpoints, request/response DTOs, validation, or exception handling.

## Recommended patterns
- Controllers:
  - `@RestController`, `@RequestMapping("/orders")`
  - Separate DTOs from entities (request/response models)
- Validation:
  - Use `@Valid` and Jakarta annotations (`@NotNull`, `@DecimalMin`)
- Error handling:
  - Centralized `@RestControllerAdvice`
  - Return stable JSON error contract
- Time:
  - Prefer `Instant` / `OffsetDateTime` in domain and ISO-8601 in API

## Project-specific rules
- API pagination is 1-based (`page`), internally convert to 0-based for Spring `PageRequest`
- Enforce `limit <= 100`
- Filters must be optional and combinable

## Commands
- Run: `./gradlew bootRun`
- Tests: `./gradlew test`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arulat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
