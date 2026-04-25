---
name: red-acceptance
description: Write ONE acceptance test following TDD (red phase). Creates disabled test using 3-tier DSL architecture. Use when user wants to create acceptance tests or mentions /red-acceptance command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /red-acceptance - Write Acceptance Test (Red Phase)

## Usage
```
/red-acceptance "Story name" "Scenario name"
/red-acceptance 4 "Login fails with incorrect password"
/red-acceptance                                        # Interactive selection
```

## Workflow

1. Load `.claude/agents/red-agent.md` + `.claude/templates/acceptance/test-class.md`
2. Read story and API spec from `ProductSpecification/`
3. Analyze existing tests in `acceptance/app-acceptance/`
4. Write ONE test with @Disabled, create/update Statements and DTOs as needed
5. Verify test compiles

## Test Types

| Type | Base Class | Tag |
|------|------------|-----|
| Backend API | `AbstractBackendTest` | `@Tag("backend")` |
| Frontend UI | `AbstractUiTest` | `@Tag("frontend")` |

Story mapping: see `.claude/shared/story-mapping.md`

**Next step:** Implement all layers, then `/green-acceptance`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
