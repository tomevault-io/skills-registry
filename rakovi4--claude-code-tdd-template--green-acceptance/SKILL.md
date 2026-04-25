---
name: green-acceptance
description: Enable a disabled acceptance test and verify it passes (TDD green phase). Runs backend, executes test, confirms GREEN state. Use when user wants to verify acceptance test implementation or mentions /green-acceptance command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /green-acceptance - Verify Acceptance Test (Green Phase)

## Usage
```
/green-acceptance "Story name" "Scenario name"
/green-acceptance "LoginLogoutAcceptanceTest" "loginFailsWithIncorrectPassword"
/green-acceptance                                        # Interactive selection
```

## Prerequisites

All layers must be implemented first: `/green-usecase`, then `/green-adapter {adapter}` for each required adapter.

## Workflow

1. Load `.claude/agents/green-agent.md` + `.claude/templates/acceptance/implementation.md`
2. Find disabled test, remove @Disabled (ONLY change)
3. Start backend: `Skill tool: skill="run-backend"`
4. Run test: `Skill tool: skill="test-acceptance", args="backend {TestClassName}"`
5. If FAILED: re-add @Disabled and analyze failure
6. Stop backend: `Skill tool: skill="stop-backend"`

Story mapping: see `.claude/shared/story-mapping.md`

**Next step:** `/refactor` or proceed to next story

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
