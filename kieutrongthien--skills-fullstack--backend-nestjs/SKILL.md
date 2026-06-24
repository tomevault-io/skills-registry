---
name: backend-nestjs
description: Build, maintain, and debug NestJS backends (TypeScript, REST/GraphQL). Use when this capability is needed.
metadata:
  author: kieutrongthien
---

# Backend - NestJS

## When to use this skill
- Creating or updating NestJS modules, controllers, services, DTOs, and pipes.
- Adding REST or GraphQL endpoints, validation, authentication, or persistence.
- Improving logging, error handling, testing, or configuration management.

## Quick start
1. Install deps: `npm ci` (or project-standard package manager).
2. Env: copy `.env.example` -> `.env`; set database URL, JWT secrets, external service keys.
3. Dev server: `npm run start:dev`; prod build: `npm run build` and `npm run start:prod`.
4. Tests: `npm test` (unit) and `npm run test:e2e` (e2e); lint: `npm run lint`.

## Core patterns
- Modules group related providers/controllers; keep dependencies minimal.
- DTOs define input/output shapes; use `class-validator` and `class-transformer` for validation and transformation.
- Controllers handle transport; services contain business logic; repositories/ORM clients manage data access.
- Use pipes for validation/transformation, filters for cross-cutting error shaping, guards for auth.

## Coding principles
- Keep controllers thin; put business logic in services; keep data access in repositories/ORM layers.
- Validate and transform all inputs via DTOs; avoid leaking domain errors—normalize with filters/interceptors.
- Use config module (no direct `process.env` reads); validate env with Joi.
- Log with correlation IDs; avoid logging secrets; prefer structured logging.
- Enforce lint/format/tests/type-check/build before merge (run `scripts/dev-check.sh`).

## Patterns and snippets
- DTO, controller/service, config, and error filter templates live in references/snippets.md.
- Prefer guards for authz, strategies for authn; keep transactions in services.
- Map ORM errors to HTTP-friendly responses; keep domain errors typed.

## Authentication and authorization
- JWT or session strategies via `@nestjs/passport` and `PassportStrategy`.
- Guards for role/permission checks; apply globally or per-route.
- Hash secrets with bcrypt/argon2; never log secrets.

## Error handling and logging
- Global `HttpExceptionFilter` to normalize errors.
- Use Nest logger or a structured logger (pino/winston) with request correlation IDs.

## Testing
- Unit: test services with in-memory fakes; use `TestingModule` to inject dependencies.
- E2E: spin app via `Test.createTestingModule`, apply pipes/filters, hit HTTP endpoints; reset database between tests.

## Bundled resources
- scripts/dev-check.sh: run pre-commit/PR to verify lint, format, unit/e2e tests, type-check, and build with the detected package manager.
- references/coding-standards.md: quick guardrails for controller/service boundaries, validation, config, errors, logging, and testing.
- references/best-practices.md: deeper guidance on module design, DTOs, error shaping, auth, logging, and testing conventions.
- references/snippets.md: templates for DTOs, controllers, config, and error filters.
- assets/pr-template.md: PR checklist covering testing, migrations, and API contract updates.
- assets/migration-checklist.md: use when adding schema changes to ensure migrations are safe and tested.

## Delivery checklist
- Lint, tests, and type checks pass.
- DTOs validate inputs; guards cover protected routes; errors are normalized.
- Config validated; secrets kept out of repo; migrations applied; health endpoint present if required.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kieutrongthien) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
