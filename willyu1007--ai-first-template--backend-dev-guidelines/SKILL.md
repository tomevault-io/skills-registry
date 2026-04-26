---
name: backend-dev-guidelines
description: Backend service patterns for Node.js/Express/TypeScript (layering, validation, config, monitoring, testing). Keywords: backend, api, express, node, typescript, service patterns. Use when this capability is needed.
metadata:
  author: willyu1007
---

# Backend Development Guidelines (Node.js / Express / TypeScript)

This skill collects reusable backend patterns for services built with Node.js, Express, and TypeScript.

---

## Purpose

- Establish a consistent structure for backend services.
- Reduce bugs by separating concerns (routing vs business logic vs data access).
- Make failures observable (error tracking + structured logs).
- Make changes testable (clear boundaries, predictable inputs/outputs).

---

## When to Use

Use this skill when working on:
- Routes / controllers / request handlers
- Service-layer business logic
- Repository/data-access patterns (e.g., Prisma)
- Middleware (auth, validation, error boundaries)
- Configuration management
- Monitoring/error tracking and performance spans
- Testing and refactoring backend code

---

## Quick Start

### New endpoint checklist

- [ ] **Route**: defines HTTP method + path + middleware only
- [ ] **Controller**: parses input, validates, calls service, formats response
- [ ] **Service**: implements business logic; no HTTP knowledge
- [ ] **Repository**: encapsulates DB access and query construction (when non-trivial)
- [ ] **Validation**: schema-validated inputs (e.g., Zod)
- [ ] **Monitoring**: capture exceptions and add key spans
- [ ] **Tests**: unit tests for service/repo; integration tests for route

### New service checklist

- [ ] `src/config/` centralized config loading/validation
- [ ] `src/middleware/` includes auth + request context + error boundary
- [ ] `src/routes/` delegates to controllers
- [ ] `src/controllers/` follows a consistent error-handling pattern
- [ ] Monitoring is initialized early in process startup

---

## Core principles (high signal)

1. **Routes only route** (no business logic in routes)
2. **Controllers handle HTTP, services handle business rules**
3. **Validate all external inputs** (params/body/query)
4. **Centralize configuration** (avoid scattered `process.env` access in app code)
5. **Capture and contextualize errors** (monitoring + structured logs)
6. **Repositories for complex DB access** (avoid scattered queries)
7. **Test at the right layer** (fast unit tests + targeted integration tests)

---

## Related Skills

| Need to… | Skill |
|---------|------|
| Understand layered architecture | `architecture-overview` |
| Build routes + controllers | `routing-and-controllers` |
| Structure services + repositories | `services-and-repositories` |
| Validate inputs | `validation-patterns` |
| Add monitoring / error tracking | `sentry-and-monitoring` |
| Create middleware | `middleware-guide` |
| Database access patterns | `database-patterns` |
| Configuration patterns | `configuration` |
| Async + error patterns | `async-and-errors` |
| Testing strategy | `testing-guide` |
| End-to-end examples | `complete-examples` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
