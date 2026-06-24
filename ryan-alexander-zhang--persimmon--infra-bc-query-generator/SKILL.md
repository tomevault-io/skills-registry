---
name: infra-bc-query-generator
description: Use when generating CQRS read-side query infrastructure (query DTO/Mapper/Impl) for a bounded context, with optional app query ports and projection write paths.
metadata:
  author: ryan-alexander-zhang
---

# Infra BC Query Generator

## Overview
Generates CQRS/read-side query infrastructure for a bounded context—lightweight query DTOs, mappers, and implementations that keep read paths separated from write-side aggregates.
**REQUIRED:** Follow `GENERATOR_SKILL_STRUCTURE.md`. Variables in `VARIABLES.md`.

Templates: See `references/templates.md`.

## When to Use
- Creating read-side query implementations under:
  - `{{basePackage}}.infra.query.{{bcName}}.dto`
  - `{{basePackage}}.infra.query.{{bcName}}.mapper`
  - `{{basePackage}}.infra.query.{{bcName}}.impl`
- Supporting application query services (`{{basePackage}}.app.{{bcName}}.query.*`)
- Adding projection write paths alongside read endpoints for new read models

### Don't use when
- Implementing write-side aggregate persistence—use `infra-bc-repository-generator`
- Generating standalone PO/Mapper for write tables—use `infra-mybatis-po-mapper-generator`
- Building web-layer DTOs for API responses—use `adapter-web-controller-generator`

## Inputs Required
- Business context name (`{{bcName}}`)
- Query use-case name (what the query returns)
- Output DTO shape (fields + paging)
- Storage source:
  - existing write table(s) or dedicated read model/projection
- Whether an app query port is needed:
  - If app wants an abstraction, generate `{{basePackage}}.app.{{bcName}}.port.*` (optional by design)

## Outputs
- Query DTO:
  - `{{infraModuleDir}}/src/main/java/{{basePackagePath}}/infra/query/{{bcName}}/dto/<XxxQueryDTO>.java`
- Mapper:
  - `.../infra/query/{{bcName}}/mapper/<XxxQueryMapper>.java`
- Implementation:
  - `.../infra/query/{{bcName}}/impl/<XxxQueryPortImpl>.java` (implements app port when used)
- When the feature introduces or changes a read model/projection table, also generate projection
  write components in infra query (`*ProjectionMapper`, `*ProjectionPortImpl`) so read/write paths
  are both present.
- Optional app port + app query DTO:
  - `{{appModuleDir}}/src/main/java/{{basePackagePath}}/app/{{bcName}}/port/<XxxQueryPort>.java`
  - `{{appModuleDir}}/src/main/java/{{basePackagePath}}/app/{{bcName}}/query/dto/<XxxResultDTO>.java`
- Pagination DTOs (`PageQuery`, `PageResult`) in `app/common/query/dto/` (generated once, reused)
- Tests:
  - Unit tests for mapping logic
  - `*IT` if query SQL is non-trivial or relies on indexes/constraints

## Naming & Packaging
- Query DTOs are infra-only; do not expose to domain.
- App query DTOs (if any) should be app-owned and stable.
- Prefer `*QueryMapper` and `*QueryPortImpl`.

## Implementation Rules
- Query paths should not load full aggregates; return lightweight DTOs.
- Keep SQL/persistence details in query mapper.
- If app defines a query port, infra implementation must only depend on app types (DTOs/ports), not adapter types.
- Time boundary logic in infra query impl must use injected `AppClock`; do not call `Instant.now()` directly.
- Do not deliver query-only code for new read models: projection write path and query read path must
  be completed in the same slice.

## Reference Implementations
- Package rules:
  - `{{infraModuleDir}}/src/main/java/{{basePackagePath}}/infra/query/package-info.java`
  - `{{infraModuleDir}}/src/main/java/{{basePackagePath}}/infra/query/{{bcName}}/impl/package-info.java`
- App port guidance:
  - `{{appModuleDir}}/src/main/java/{{basePackagePath}}/app/{{bcName}}/port/package-info.java`

## Tests
- Prefer unit tests for mapping; use `*IT` for SQL correctness.
- Add unit tests for query-service normalization logic (default page/size/limit clamping) when such logic exists.

## Common Mistakes
| Mistake | Why It Happens | Fix |
|---------|---------------|-----|
| Reusing infra query DTOs as web DTOs | Shortcut to avoid creating adapter-layer DTOs | Adapter owns web DTOs; infra query DTOs stay in infra |
| Depending on domain aggregates in read models | Loading full aggregates for convenience | Query paths return lightweight DTOs; never load aggregates |
| Shipping read endpoints without projection write path | Delivering query-only without upsert/update logic | Complete both projection write and query read in the same slice |
| Calling `Instant.now()` directly in query impl | Forgetting testability requirements | Inject `AppClock` and use it for all time boundary logic |

## Phase Commit Gate

Before committing generated/updated code:
- Ensure compile and unit tests pass (minimum: `mvn -q clean test`).
- If DB behavior/schema changes are involved, run `mvn -q clean verify` to execute `*IT`.
- Invoke `requesting-code-review` for the intended commit scope and resolve Critical/Important findings.
- Prefer one focused commit per phase/slice (do not batch unrelated changes).

## Integration
- **Called by:** `scaffold-router`, `dev-workflow-ddd-implementation-workflow`
- **Pairs with:** `infra-flyway-migration-generator`, `infra-mybatis-po-mapper-generator`, `infra-it-db-generator`, `app-port-generator`, `adapter-web-controller-generator`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryan-alexander-zhang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
