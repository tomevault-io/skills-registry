---
name: red-selenium
description: Write ONE Selenium browser test following TDD (red phase). Creates test extending AbstractUiTest with @Tag("frontend"). Use when user wants to create Selenium UI tests or mentions /red-selenium command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /red-selenium - Write Selenium Browser Test (Red Phase)

Orchestrates red-agent for selenium layer.

## Usage
```
/red-selenium "Story name" "Scenario name"
/red-selenium 2 "User sees registration form"
/red-selenium                                          # Interactive selection
```

## CRITICAL TDD RULE

Test MUST be run WITHOUT @Disabled first to verify failure.
Only after confirming failure is correct, add @Disabled annotation.

## Prerequisites

- Backend must be running (`/run-backend`)
- Frontend must be running (`/run-frontend`)

## Workflow

1. Parse user input (story, scenario)
2. Load agent instructions from `.claude/agents/red-agent.md`
3. Load layer template from `.claude/templates/frontend/selenium-test.md`
4. Execute the following steps:
   - Read story spec and mockups from `ProductSpecification/`
   - Analyze existing tests in `acceptance/app-acceptance/`
   - **Predict expected failure** (document before writing test)
   - Write ONE test extending `AbstractUiTest` with `@Tag("frontend")` **WITHOUT @Disabled**
   - Run test using `Skill tool: skill="test-acceptance", args="frontend {TestClassName}"`
   - **Verify failure matches prediction**
   - Add `@Disabled` with actual failure reason
   - Verify test is now SKIPPED
5. Return result summary with predicted vs actual failure

## Key Rules

- ONE test per invocation
- **MUST verify failure before disabling**
- Extend `AbstractUiTest` with `@Tag("frontend")`
- Use `data-testid` locators for element selection
- Test via browser only (no direct API calls in test)
- Use Skill tool for test execution (not gradle directly)

## Expected Failure Patterns

| Current Implementation | Expected Test Failure |
|----------------------|----------------------|
| No page/component | Element not found / timeout |
| Missing `data-testid` | `NoSuchElementException` |
| Wrong navigation | URL assertion failure |
| Missing form handling | Interaction failure |

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

After red phase: `/green-selenium "Story name" "Scenario name"`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
