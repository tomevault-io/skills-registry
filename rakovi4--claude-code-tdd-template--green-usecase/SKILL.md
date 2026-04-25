---
name: green-usecase
description: Implement use-case code to make ONE disabled test pass (TDD green phase). Reads test, implements minimal production code, enables test, verifies green state. Use when user wants to implement use-case logic or mentions /green-usecase command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /green-usecase - Implement Use-Case (Green Phase)

## Usage
```
/green-usecase "Story name" "Scenario name"
/green-usecase 1 "User logs in with valid credentials"
/green-usecase                                        # Interactive selection
```

## Workflow

1. Load `.claude/agents/green-agent.md` + `.claude/templates/usecase/implementation.md`
2. Find disabled test in `backend/usecase/src/test/`, read it (**READ-ONLY**)
3. Implement minimal production code in `backend/usecase/src/main/`
4. Remove @Disabled (ONLY allowed test change)
5. Run test: `Skill tool: skill="test-usecase", args="{TestClassName}"`
6. Verify GREEN + no regression: `Skill tool: skill="test-usecase"`

Story mapping: see `.claude/shared/story-mapping.md`

**Next step:** `/refactor` or proceed to next scenario

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
