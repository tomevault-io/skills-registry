---
name: ringdev-frontend-e2e
description: Semantic selectors used Use when this capability is needed.
metadata:
  author: lerianstudio
---

# Dev Frontend E2E Testing (Gate 5)

## Overview

Ensure all user flows from `ring:product-designer` have passing **Playwright E2E tests** across Chromium, Firefox, and WebKit with responsive viewport coverage.

**Core principle:** If the product-designer defined a user flow, it must have an E2E test. If the user can encounter an error, it must be tested.

<block_condition>
- Untested user flow = FAIL
- No error path tests = FAIL
- Fails on any browser = FAIL
- Flaky tests (fail on consecutive runs) = FAIL
</block_condition>

## CRITICAL: Role Clarification

**This skill ORCHESTRATES. Frontend QA Analyst Agent (e2e mode) EXECUTES.**

| Who | Responsibility |
|-----|----------------|
| **This Skill** | Gather requirements, dispatch agent, track iterations |
| **QA Analyst Frontend Agent** | Write Playwright tests, run cross-browser, verify flows |

---

## Standards Reference

**MANDATORY:** Load testing-e2e.md standards via WebFetch.

<fetch_required>
https://raw.githubusercontent.com/LerianStudio/ring/main/dev-team/docs/standards/frontend/testing-e2e.md
</fetch_required>

---

## Step 1: Validate Input

```text
REQUIRED INPUT:
- unit_id: [task/subtask being tested]
- implementation_files: [files from Gate 0]

OPTIONAL INPUT:
- user_flows_path: [path to user-flows.md]
- backend_handoff: [endpoints, contracts from backend cycle]
- gate4_handoff: [full Gate 4 output]

if any REQUIRED input is missing:
  → STOP and report: "Missing required input: [field]"

if user_flows_path provided:
  → Load user flows as E2E test scenarios
  → All flows MUST be covered

if backend_handoff provided:
  → Verify E2E tests exercise backend endpoints
  → Verify request payloads match contracts
```

## Step 2: Dispatch Frontend QA Analyst Agent (E2E Mode)

```text
Task tool:
  subagent_type: "ring:qa-analyst-frontend"
  prompt: |
    **MODE:** E2E TESTING (Gate 5)

    **Standards:** Load testing-e2e.md

    **Input:**
    - Unit ID: {unit_id}
    - Implementation Files: {implementation_files}
    - User Flows: {user_flows_path or "N/A"}
    - Backend Handoff: {backend_handoff or "N/A"}

    **Requirements:**
    1. Create Playwright tests for all user flows
    2. Test happy path + error paths (API 500, timeout, validation)
    3. Run on Chromium, Firefox, WebKit
    4. Test responsive viewports (mobile, tablet, desktop)
    5. Use data-testid or semantic role selectors only
    6. Run 3x consecutively to verify no flaky tests

    **Output Sections Required:**
    - ## E2E Testing Summary
    - ## Flow Coverage
    - ## Handoff to Next Gate
```

## Step 3: Evaluate Results

```text
Parse agent output:

if "Status: PASS" in output:
  → Gate 5 PASSED
  → Return success with metrics

if "Status: FAIL" in output:
  → If flow not covered: re-dispatch agent to add missing tests
  → If browser failure: re-dispatch implementation agent to fix
  → If flaky: re-dispatch agent to stabilize tests
  → Re-run E2E tests (max 3 iterations)
  → If still failing: ESCALATE to user
```

## Step 4: Generate Output

```text
## E2E Testing Summary
**Status:** {PASS|FAIL}
**Flows Tested:** {X/Y}
**Happy Path Tests:** {count}
**Error Path Tests:** {count}
**Browsers Passed:** {X/3}
**Consecutive Passes:** {X/3}

## Flow Coverage
| User Flow | Happy Path | Error Paths | Browsers | Viewports | Status |
|-----------|------------|-------------|----------|-----------|--------|
| {flow} | {PASS|FAIL} | {descriptions} | {X/3} | {X/3} | {PASS|FAIL} |

## Backend Handoff Verification
| Endpoint | Method | Contract Verified | Status |
|----------|--------|-------------------|--------|
| {endpoint} | {method} | {fields} | {PASS|FAIL} |

## Handoff to Next Gate
- Ready for Gate 6 (Performance Testing): {YES|NO}
- Iterations: {count}
```

---

## Severity Calibration

| Severity | Criteria | Examples |
|----------|----------|----------|
| **CRITICAL** | User flow completely broken, data corruption | Transaction fails, user data lost, checkout blocked |
| **HIGH** | Missing user flow tests, flaky tests, browser failure | Untested flow from product-designer, test fails intermittently |
| **MEDIUM** | Error path gaps, viewport issues | Missing error handling test, mobile viewport untested |
| **LOW** | Selector improvements, test organization | Non-semantic selectors, test file structure |

Report all severities. CRITICAL = immediate fix. HIGH = fix before gate pass. MEDIUM = fix in iteration. LOW = document.

---

## Anti-Rationalization Table

See [shared-patterns/shared-anti-rationalization.md](../shared-patterns/shared-anti-rationalization.md) for universal anti-rationalizations. Gate-specific:

| Rationalization | Why It's WRONG | Required Action |
|-----------------|----------------|-----------------|
| "Unit tests cover the flow" | Unit tests don't test real browser interaction. | **Write E2E tests** |
| "Only Chromium matters" | Firefox and Safari have different behavior. | **Test all 3 browsers** |
| "Happy path is enough" | Users encounter errors. Test error handling. | **Add error path tests** |
| "CSS selectors are fine" | CSS changes with refactors. Use semantic selectors. | **Use roles and testids** |
| "Product-designer flows are suggestions" | Flows define acceptance criteria. Cover all. | **Test all flows** |
| "Test is flaky, skip it" | Flaky tests indicate real instability. | **Fix the test** |

---

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lerianstudio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
