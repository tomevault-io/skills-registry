---
name: red-frontend
description: Write ONE Vitest logic test following TDD (red phase). Creates test for pure frontend logic (.logic.ts). Use when user wants to create frontend logic tests or mentions /red-frontend command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /red-frontend - Write Frontend Logic Test (Red Phase)

Orchestrates red-agent for frontend-logic layer.

## Usage
```
/red-frontend "Story name" "Scenario name"
/red-frontend 2 "Validate email"
/red-frontend                          # Interactive selection
```

## CRITICAL TDD RULE

Test MUST be run WITHOUT .skip first to verify failure.
Only after confirming failure is correct, add .skip marker.

## Workflow

1. Parse user input (story, scenario)
2. Load agent instructions from `.claude/agents/red-agent.md`
3. Load layer template from `.claude/templates/frontend/logic-test.md`
4. Execute the following steps:
   - Read story spec from `ProductSpecification/stories/{story}/`
   - Analyze existing tests in `frontend/src/`
   - **Predict expected failure** (document before writing test)
   - Write ONE test **WITHOUT .skip** initially
   - Run test using `Skill tool: skill="test-frontend", args="{test-file-pattern}"`
   - **Verify failure matches prediction**
   - Add `.skip` to the test
   - Verify test is now SKIPPED
5. Return result summary with predicted vs actual failure

## Key Rules

- ONE test per invocation
- **ALWAYS predict failure BEFORE running**
- Verify RED state before adding .skip
- Pure functions only in `.logic.ts` files (no React, no fetch)
- Use `it.skip(...)` to disable after verifying failure
- Use Skill tool for test execution (not npx directly)

## Expected Failure Patterns

| Current Implementation | Expected Test Failure |
|----------------------|----------------------|
| No module file | `Cannot find module` |
| `throw new Error('Not implemented')` | `Error: Not implemented` |
| Function returns `undefined` | Assertion failure |
| Returns wrong value | Assertion comparison failure |

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
| 09 | Task Detail View |
| 10 | Activity Log |
| 11 | Dashboard |
| 12 | Billing & Subscription |

## Next Step

After red phase: `/green-frontend "Story name" "Scenario name"`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
