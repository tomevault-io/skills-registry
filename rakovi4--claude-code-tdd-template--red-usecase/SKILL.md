---
name: red-usecase
description: Write ONE use-case test following TDD (red phase). Creates test with 3-tier architecture (test class -> statements -> scope). Use when user wants to create use-case tests or mentions /red-usecase command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /red-usecase - Write Use-Case Test (Red Phase)

Orchestrates red-agent for usecase layer.

## Usage
```
/red-usecase "Story name" "Scenario name"
/red-usecase 1 "Successful login"
/red-usecase                          # Interactive selection
```

## Workflow

1. Parse user input (story, scenario)
2. Load agent instructions from `.claude/agents/red-agent.md`
3. Load layer template from `.claude/templates/usecase/test-class.md`
4. Execute the following steps:
   - Read story spec from `ProductSpecification/stories/{story}/`
   - Analyze existing tests in `backend/usecase/src/test/`
   - Predict expected failure
   - Write ONE test WITHOUT @Disabled initially
   - Run test using `Skill tool: skill="test-usecase", args="{TestClassName}"`
   - Act when failure doesn't match prediction
   - Add @Disabled annotation with actual failure reason
   - Verify test is now SKIPPED
5. Return result summary

## Key Rules

- ONE test per invocation
- ALWAYS predict failure BEFORE running
- Verify RED state before disabling
- Follow 3-tier architecture (Test -> Statements -> Scope)
- Use Skill tool for test execution (not gradle directly)

## Story Mapping

| # | Story |
|---|-------|
| 01 | Login/Logout |
| 02 | Registration |
| 03 | Password Reset |
| 04 | Connect Calendar |
| 05 | Create Task |
| 06 | Edit Task |
| 07 | Delete Task |
| 08 | Archive/Restore Task |
| 09 | Task detail view |
| 10 | Activity Log |
| 11 | Dashboard |
| 12 | Billing & Subscription |

## Next Step

After red phase: `/green-usecase "Story name" "Scenario name"`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
