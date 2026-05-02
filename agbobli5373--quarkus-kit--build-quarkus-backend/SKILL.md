---
name: build-quarkus-backend
description: Build, refactor, and harden Java Quarkus backend services for production. Use when implementing REST APIs, domain logic, persistence, security, testing, observability, performance tuning, or deployment-safe changes in Quarkus applications. Use Context7 MCP when version-sensitive or API-specific documentation must be confirmed. Use when this capability is needed.
metadata:
  author: agbobli5373
---

# Build Quarkus Backend

## Overview

Use this skill to deliver production-ready Quarkus backend work with consistent architecture, safe defaults, and measurable quality gates.

Prefer incremental changes that preserve behavior, add tests, and keep startup time and memory usage predictable.
Prefer modern Java language features supported by the project baseline.
For Quarkus `3.31+` (GA on January 28, 2026), default to Java `25` unless project constraints require otherwise.

## Workflow

1. Define scope and constraints.
2. Inspect current project conventions before coding.
3. Select modern Java features that improve clarity and safety.
4. Implement with Quarkus-first patterns.
5. Validate with tests and runtime checks.
6. Summarize risks, tradeoffs, and follow-up actions.

## Reference Index

- `references/quarkus-backend-patterns.md`
  - Use for core service/API/transaction/observability design decisions.
- `references/quarkus-testing-matrix.md`
  - Use when choosing between unit, `@QuarkusTest`, Testcontainers, and native test scopes.
- `references/quarkus-security-jwt-oidc.md`
  - Use for JWT/OIDC setup, endpoint authorization, and security test coverage.
- `references/quarkus-panache-vs-repository.md`
  - Use when deciding or refactoring persistence strategy (Panache vs explicit repository).
- `references/quarkus-delivery-checklist.md`
  - Use before delivery to confirm API/domain/security/testing completeness.
- `references/quarkus-prod-runbook.md`
  - Use for production readiness, rollout, rollback, and post-deploy verification.
- `scripts/quarkus-quality-gate.sh`
  - Use to run deterministic build/test/static checks (`--native` for native packaging checks).

## Context7 Documentation Rules

- Use Context7 MCP when any change depends on version-specific behavior:
  - Quarkus extension behavior, config keys, or runtime differences.
  - Java language/JDK API behavior tied to version (especially Java 21 vs 25 decisions).
  - Security configuration semantics (OIDC/JWT claims, auth config properties).
  - Native build/runtime options and compatibility constraints.
- Use Context7 MCP when memory is uncertain about exact annotations, config names, or defaults.
- Prefer official documentation sources through Context7 results (Quarkus docs, OpenJDK/JEP docs, Jakarta/SmallRye/MicroProfile docs).
- Report the key docs consulted when the implementation is version-sensitive.
- Fall back to local project conventions only when docs are unavailable or task scope is intentionally internal.

## 1) Define Scope and Constraints

- Confirm Java and Quarkus versions from `pom.xml` or `build.gradle`.
- If Quarkus is `3.31+`, assume Java `25` is supported and choose it by default for new work.
- Use Context7 MCP to confirm version-specific docs before design decisions that affect compatibility.
- Confirm Java feature baseline and policy for preview features before introducing new syntax.
- Confirm runtime profile targets (`dev`, `test`, `prod`) and environment assumptions.
- Confirm datastore and integration boundaries (PostgreSQL, Kafka, external HTTP, etc.).
- Capture non-functional requirements: latency, throughput, security, and compliance constraints.

## 2) Inspect Existing Conventions

- Reuse existing package layout and naming conventions.
- Reuse project standards for DTOs, mapping, error handling, and logging.
- Reuse existing test style before introducing new patterns.
- Keep changes minimal and local unless a broader refactor is explicitly requested.

## 3) Select Modern Java Features Intentionally

- Prefer `record` for immutable DTOs, command objects, and value types.
- Prefer sealed interfaces/classes for finite domain hierarchies.
- Prefer switch expressions and pattern matching when they reduce branching noise.
- Use text blocks for readable multi-line SQL, JSON, and templates.
- Use `Optional` for return values, not for entity fields or DTO properties.
- Use `var` only when the inferred type is obvious from the right-hand side.
- Use `Instant`, `Duration`, and `OffsetDateTime` APIs instead of legacy date/time classes.
- Consider virtual threads for blocking I/O paths only after checking Quarkus compatibility and load profile.

## 4) Implement with Quarkus-First Patterns

- Prefer CDI and constructor injection.
- Keep REST resources thin; push business rules into services.
- Keep transactional boundaries explicit (`@Transactional`) at service layer.
- Use Bean Validation (`jakarta.validation`) at input boundaries.
- Return stable API contracts with explicit DTOs rather than exposing persistence entities directly.
- Prefer explicit status codes and structured error responses.
- Keep configuration externalized via `application.properties` and typed config where useful.
- Use the Reference Index above to load only the docs needed for the current task.
- Use Context7 MCP when implementation requires exact configuration keys or extension behavior confirmation.

## 5) Validate and Quality-Gate

- Run unit and integration tests for touched behavior.
- Add regression tests for bugs and edge cases before or with the fix.
- Check serialization, validation, and error paths for API endpoints.
- Verify persistence behavior in success and failure scenarios.
- Verify profile-specific configuration is safe for production defaults.
- Verify modern Java features compile and run on the project Java runtime in CI and production.
- Run the deterministic gate script from this skill:
  - `scripts/quarkus-quality-gate.sh --project-dir <backend-service-path>`
  - Add `--native` for native image checks when requested.
  - Add `--skip-integration` only when the task explicitly excludes integration coverage.
- Use the Reference Index above for delivery and production validation checklists.

## 6) Delivery Expectations

- Report exactly what changed and why.
- Call out schema, API, and configuration impacts.
- Call out persistence strategy decisions (Panache vs repository) when persistence code changes.
- Call out Context7 documentation consulted for version-sensitive choices.
- Identify residual risks, missing tests, and rollback considerations.
- Propose smallest sensible next steps when useful.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agbobli5373) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
