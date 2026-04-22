---
name: domain-model-generator
description: Use when creating or modifying domain model types (Aggregate, Entity, VO, Enum, Exception) in the domain module. No Lombok, minimal API surface.
metadata:
  author: ryan-alexander-zhang
---

# Domain Model Generator

## Overview
Generates framework-free domain model types with validated invariants and minimal public API.
**REQUIRED:** Follow `GENERATOR_SKILL_STRUCTURE.md`. Variables in `VARIABLES.md`.

Templates: See `references/templates.md`.

## When to Use
- New aggregates/entities/value objects/enums under `{{domainModuleDir}}`
- Domain exceptions and domain-level constants

### Don't use when
- The type belongs to infra (PO/Mapper) or app (DTO/Command) — use the corresponding layer skill instead.
- You need a repository interface — use `domain-repository-port-generator`.
- The type is a cross-layer shared DTO — it likely belongs in app or adapter.

## Inputs Required
- Target package under `{{basePackage}}.domain.{{bcName}}.*` (BC-first) or `{{basePackage}}.domain.common.*` (cross-cutting)
- Type kind: `Aggregate/Entity/VO/Enum/Exception`
- Public API expectations (which getters are truly needed)
- Invariant rules (validation, allowed transitions)

## Outputs
- `{{domainModuleDir}}/src/main/java/{{basePackagePath}}/.../<Type>.java`
- Optional: `{{domainModuleDir}}/src/test/java/{{basePackagePath}}/.../<Type>Test.java`

## Naming & Packaging
- Business semantics go under `{{basePackage}}.domain.{{bcName}}.*`
- Cross-cutting goes under `{{basePackage}}.domain.common.*`
- Prefer `*Id` / `*Type` / `*Status` for identity/type/status.

## Rules
- No Lombok annotations.
- Expose only necessary getters; prefer immutability where reasonable.
- Validate invariants in constructors/factories.
- Keep domain types framework-free.

## Reference Implementations
- `{{domainModuleDir}}/src/main/java/{{basePackagePath}}/domain/common/workflow/WorkflowInstance.java`
- `{{domainModuleDir}}/src/main/java/{{basePackagePath}}/domain/common/workflow/WorkflowStepStatus.java`
- `{{domainModuleDir}}/src/main/java/{{basePackagePath}}/domain/common/event/DomainEvent.java`

## Tests
- Add unit tests when invariants or parsing logic exist; keep tests framework-free.

## Common Mistakes
| Mistake | Why It Happens | Fix |
|---------|---------------|-----|
| Adding convenience methods that leak policy | Trying to be helpful with domain logic shortcuts | Keep API minimal; policy belongs in app-layer handlers |
| Putting business rules in `domain.common` | Mistaking shared types for shared rules | Only truly cross-cutting types go in `common`; BC-specific rules stay in `domain.{{bcName}}` |
| Using Lombok annotations | Habit from other layers | Domain types must be Lombok-free; write explicit constructors/getters |
| Exposing mutable collections | Returning internal lists directly | Return unmodifiable copies or views |

## Checklist
- [ ] Located under `{{basePackage}}.domain.**`
- [ ] No Lombok
- [ ] Minimal methods, meaningful names
- [ ] Unit tests for invariants when non-trivial

## Integration
- **Called by:** `scaffold-router`, `dev-workflow-ddd-implementation-workflow`
- **Pairs with:** `domain-repository-port-generator` (ports for aggregates), `app-usecase-generator` (handlers that orchestrate domain types), `java-unit-test-authoring` (invariant tests)

## Commit Gate

- pass required tests (`mvn -q clean test` minimum, `mvn -q clean verify` if DB behavior changed)
- run `requesting-code-review`
- resolve Critical/Important findings
- keep the commit focused

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryan-alexander-zhang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
