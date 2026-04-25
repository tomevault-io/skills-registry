---
name: red-adapter
description: Write ONE adapter test following TDD (red phase). First argument is adapter name (matches directory under backend/adapters/), then story and scenario. Use when user wants to write adapter tests or mentions /red-adapter command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /red-adapter - Write Adapter Test (Red Phase)

## Usage
```
/red-adapter rest "Story name" "Scenario name"
/red-adapter h2 1 "User logs in with valid credentials"
/red-adapter external-api 4 "CalendarService rejects invalid token"
/red-adapter payment                                        # Interactive selection
```

First argument is the adapter name. Remaining arguments are story and scenario.

## CRITICAL TDD RULE

Test MUST be run WITHOUT @Disabled first to verify failure.
Only after confirming failure is correct, add @Disabled annotation.

## Convention

Adapter name resolves all paths:
- **Template:** `.claude/templates/{adapter}/test-class.md`
- **Test dir:** `backend/adapters/{adapter}/src/test/`
- **Prod dir:** `backend/adapters/{adapter}/src/main/`
- **Test command:** `Skill tool: skill="test-adapter", args="{adapter} {TestClassName}"`

## Workflow

1. Parse arguments: adapter name, story, scenario
2. Load agent instructions from `.claude/agents/red-agent.md`
3. Load layer template from `.claude/templates/{adapter}/test-class.md`
4. Execute the following steps:
   - Identify methods needed for scenario
   - **Predict expected failure** (document before writing test)
   - Write ONE test **WITHOUT @Disabled**
   - Run test using `Skill tool: skill="test-adapter", args="{adapter} {TestClassName}"`
   - **Act when failure doesn't match prediction**
   - Add @Disabled with actual failure reason
   - Verify test is now SKIPPED
   - Verify no regression with `Skill tool: skill="test-adapter", args="{adapter}"`
5. Return result summary with predicted vs actual failure

## Key Rules

- ONE test per invocation
- **MUST verify failure before disabling**
- @Disabled message must reflect actual failure
- Use Skill tool for test execution (not gradle directly)

Story mapping: see `.claude/shared/story-mapping.md`

## Next Step

After red phase: `/green-adapter {adapter} "Story name" "Scenario name"`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
