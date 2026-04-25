---
name: green-frontend
description: Implement frontend logic to make ONE skipped Vitest test pass (TDD green phase). Reads test, implements minimal .logic.ts code, removes .skip, verifies green state. Use when user wants to implement frontend logic or mentions /green-frontend command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /green-frontend - Implement Frontend Logic (Green Phase)

Orchestrates green-agent for frontend-logic layer.

## Usage
```
/green-frontend "Story name" "Scenario name"
/green-frontend 2 "Validate email"
/green-frontend                                        # Interactive selection
```

## Workflow

1. Parse user input (story, scenario)
2. Load agent instructions from `.claude/agents/green-agent.md`
3. Load layer template from `.claude/templates/frontend/implementation.md`
4. Execute the following steps:
   - Find skipped test (`.skip`) in `frontend/src/`
   - Read test (understand expectations) - **READ-ONLY**
   - Implement minimal production code in `.logic.ts` file
   - Remove `.skip` from the test (ONLY allowed test change)
   - Run test using `Skill tool: skill="test-frontend", args="{test-file-pattern}"`
   - Verify GREEN state
   - Run all tests using `Skill tool: skill="test-frontend"`
   - Verify no regression
5. Return result summary

## Key Rules

- **TESTS ARE READ-ONLY** - only remove `.skip`
- Implement MINIMAL code to pass the test
- Pure functions only in `.logic.ts` (no React, no fetch, no side effects)
- Use Skill tool for test execution (not npx directly)

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

After green phase: `/refactor` or proceed to next scenario

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
