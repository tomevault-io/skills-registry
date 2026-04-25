---
name: green-adapter
description: Implement adapter code to make ONE disabled test pass (TDD green phase). First argument is adapter name (matches directory under backend/adapters/), then story and scenario. Use when user wants to implement adapter logic or mentions /green-adapter command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /green-adapter - Implement Adapter (Green Phase)

## Usage
```
/green-adapter rest "Story name" "Scenario name"
/green-adapter h2 1 "User logs in with valid credentials"
/green-adapter external-api 4 "CalendarService accepts valid token"
/green-adapter payment                                        # Interactive selection
```

First argument is the adapter name. Remaining arguments are story and scenario.

## Convention

Adapter name resolves all paths:
- **Template:** `.claude/templates/{adapter}/implementation.md`
- **Test dir:** `backend/adapters/{adapter}/src/test/`
- **Prod dir:** `backend/adapters/{adapter}/src/main/`
- **Test command:** `Skill tool: skill="test-adapter", args="{adapter} {TestClassName}"`

## Workflow

1. Parse arguments: adapter name, story, scenario
2. Load agent instructions from `.claude/agents/green-agent.md`
3. Load layer template from `.claude/templates/{adapter}/implementation.md`
4. Find disabled test in `backend/adapters/{adapter}/src/test/`, read it (**READ-ONLY**)
5. Implement minimal production code
6. Remove @Disabled (ONLY allowed test change)
7. Run test: `Skill tool: skill="test-adapter", args="{adapter} {TestClassName}"`
8. Verify GREEN + no regression: `Skill tool: skill="test-adapter", args="{adapter}"`

## Key Rules

- **TESTS ARE READ-ONLY** - only remove @Disabled
- Implement MINIMAL code to pass the test
- Use Skill tool for test execution (not gradle directly)

Story mapping: see `.claude/shared/story-mapping.md`

## Next Step

After green phase: `/refactor` or proceed to next layer

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
