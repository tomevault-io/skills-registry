---
name: green-selenium
description: Enable a disabled Selenium test and verify it passes (TDD green phase). Checks prerequisites, removes @Disabled, runs browser test, confirms GREEN state. Use when user wants to verify Selenium test implementation or mentions /green-selenium command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /green-selenium - Verify Selenium Browser Test (Green Phase)

Orchestrates green-agent for selenium layer.

## Usage
```
/green-selenium "Story name" "Scenario name"
/green-selenium "RegistrationUiTest" "userSeesRegistrationForm"
/green-selenium                                        # Interactive selection
```

## Prerequisites

Before enabling Selenium test, ensure:

1. Frontend logic implemented (all `/green-frontend` tests pass)
2. Frontend API client implemented (all `/green-frontend-api` tests pass)
3. Backend running (`/run-backend`)
4. Frontend running (`/run-frontend`)
5. React component renders correctly with all `data-testid` attributes

## Workflow

1. Parse user input (test class, method name)
2. Load agent instructions from `.claude/agents/green-agent.md`
3. Execute the following steps:
   - Find disabled test in `acceptance/app-acceptance/`
   - Read test (understand expectations) - **READ-ONLY**
   - Verify prerequisites: logic + API tests pass, backend + frontend running
   - Remove `@Disabled` annotation (ONLY allowed test change)
   - Run test using `Skill tool: skill="test-acceptance", args="frontend {TestClassName}"`
   - Verify GREEN state
   - If FAILED: re-add @Disabled and analyze failure
     - May need to implement React component changes
     - May need to add missing `data-testid` attributes
     - May need to fix routing or navigation
4. Return result summary

## Key Rules

- **TESTS ARE READ-ONLY** - only remove @Disabled
- If test fails, re-add @Disabled and report what needs fixing
- Must have backend AND frontend running before test execution
- All frontend logic and API tests must pass before attempting
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
| 09 | Task Detail View |
| 10 | Activity Log |
| 11 | Dashboard |
| 12 | Billing & Subscription |

## Next Step

After green phase: commit or `/refactor`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
