---
name: red-frontend-api
description: Write ONE Vitest + MSW API client test following TDD (red phase). Creates test for frontend API client using MSW for HTTP mocking. Use when user wants to create frontend API tests or mentions /red-frontend-api command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /red-frontend-api - Write Frontend API Client Test (Red Phase)

Orchestrates red-agent for frontend-api layer.

## Usage
```
/red-frontend-api "Story name" "Scenario name"
/red-frontend-api 2 "Registration API call"
/red-frontend-api                                      # Interactive selection
```

## CRITICAL TDD RULE

Test MUST be run WITHOUT .skip first to verify failure.
Only after confirming failure is correct, add .skip marker.

## Workflow

1. Parse user input (story, scenario)
2. Load agent instructions from `.claude/agents/red-agent.md`
3. Load layer template from `.claude/templates/frontend/api-test.md`
4. Execute the following steps:
   - Identify API endpoints from `ProductSpecification/` and backend controllers
   - **Predict expected failure** (document before writing test)
   - Write ONE test with MSW handler **WITHOUT .skip** initially
   - Run test using `Skill tool: skill="test-frontend", args="{test-file-pattern}"`
   - **Verify failure matches prediction**
   - Add `.skip` to the test
   - Verify test is now SKIPPED
5. Return result summary with predicted vs actual failure

## Key Rules

- ONE test per invocation
- **MUST verify failure before adding .skip**
- Use MSW (Mock Service Worker) for HTTP mocking (analogous to WireMock on backend)
- MSW handlers define expected request/response pairs
- Use Skill tool for test execution (not npx directly)

## Expected Failure Patterns

| Current Implementation | Expected Test Failure |
|----------------------|----------------------|
| No API client module | `Cannot find module` |
| `throw new Error('Not implemented')` | `Error: Not implemented` |
| Function returns `undefined` | Assertion failure |
| Wrong URL or method | MSW unhandled request warning + failure |
| Missing error handling | Assertion on error response fails |

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

After red phase: `/green-frontend-api "Story name" "Scenario name"`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
