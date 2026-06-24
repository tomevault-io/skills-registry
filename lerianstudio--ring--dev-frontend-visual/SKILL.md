---
name: ringdev-frontend-visual
description: Snapshot files generated Use when this capability is needed.
metadata:
  author: lerianstudio
---

# Dev Frontend Visual Testing (Gate 4)

## Overview

Ensure all frontend components have **snapshot tests** covering all states, responsive viewports, and edge cases. Detect visual regressions before code review.

**Core principle:** If a user can see it, it must have a snapshot. All states, all viewports.

<block_condition>
- Missing state snapshots = FAIL
- Snapshot test failures = FAIL
- sindarian-ui component duplicated in shadcn = FAIL
</block_condition>

## CRITICAL: Role Clarification

**This skill ORCHESTRATES. Frontend QA Analyst Agent (visual mode) EXECUTES.**

| Who | Responsibility |
|-----|----------------|
| **This Skill** | Gather requirements, dispatch agent, track iterations |
| **QA Analyst Frontend Agent** | Write snapshot tests, verify states, check duplication |

---

## Standards Reference

**MANDATORY:** Load testing-visual.md standards via WebFetch.

<fetch_required>
https://raw.githubusercontent.com/LerianStudio/ring/main/dev-team/docs/standards/frontend/testing-visual.md
</fetch_required>

---

## Step 1: Validate Input

```text
REQUIRED INPUT:
- unit_id: [task/subtask being tested]
- implementation_files: [files from Gate 0]

OPTIONAL INPUT:
- ux_criteria_path: [path to ux-criteria.md]
- gate3_handoff: [full Gate 3 output]

if any REQUIRED input is missing:
  → STOP and report: "Missing required input: [field]"
```

## Step 2: Dispatch Frontend QA Analyst Agent (Visual Mode)

```text
Task tool:
  subagent_type: "ring:qa-analyst-frontend"
  prompt: |
    **MODE:** VISUAL TESTING (Gate 4)

    **Standards:** Load testing-visual.md

    **Input:**
    - Unit ID: {unit_id}
    - Implementation Files: {implementation_files}
    - UX Criteria: {ux_criteria_path or "N/A"}

    **Requirements:**
    1. Create snapshot tests for all components
    2. Cover all states (Default, Empty, Loading, Error, Success, Disabled)
    3. Add responsive snapshots (375px, 768px, 1280px) for layout components
    4. Test edge cases (long text, 0 items, special characters)
    5. Verify no sindarian-ui component duplication in components/ui/

    **Output Sections Required:**
    - ## Visual Testing Summary
    - ## Snapshot Coverage
    - ## Component Duplication Check
    - ## Handoff to Next Gate
```

## Step 3: Evaluate Results

```text
Parse agent output:

if "Status: PASS" in output:
  → Gate 4 PASSED
  → Return success with metrics

if "Status: FAIL" in output:
  → If missing snapshots: re-dispatch agent to add missing
  → If duplication found: re-dispatch implementation agent to fix
  → Re-run visual tests (max 3 iterations)
  → If still failing: ESCALATE to user
```

## Step 4: Generate Output

```text
## Visual Testing Summary
**Status:** {PASS|FAIL}
**Components with Snapshots:** {count}
**Total Snapshots:** {count}
**Snapshot Failures:** {count}

## Snapshot Coverage
| Component | States | Viewports | Edge Cases | Status |
|-----------|--------|-----------|------------|--------|
| {component} | {X/Y} | {X/Y or N/A} | {description} | {PASS|FAIL} |

## Component Duplication Check
| Component in components/ui/ | In sindarian-ui? | Status |
|-----------------------------|------------------|--------|
| {component} | {Yes|No} | {PASS|FAIL} |

## Handoff to Next Gate
- Ready for Gate 5 (E2E Testing): {YES|NO}
- Iterations: {count}
```

---

## Severity Calibration

| Severity | Criteria | Examples |
|----------|----------|----------|
| **CRITICAL** | Component duplication, major visual regression | sindarian-ui duplicated in shadcn, layout completely broken |
| **HIGH** | Missing state snapshots, snapshot failures | No loading/error state snapshot, unexpected diff |
| **MEDIUM** | Incomplete coverage, minor visual issues | Missing viewport size, edge case not tested |
| **LOW** | Documentation, test organization | Missing snapshot descriptions, file naming |

Report all severities. CRITICAL = immediate fix. HIGH = fix before gate pass. MEDIUM = fix in iteration. LOW = document.

---

## Anti-Rationalization Table

See [shared-patterns/shared-anti-rationalization.md](../shared-patterns/shared-anti-rationalization.md) for universal anti-rationalizations. Gate-specific:

| Rationalization | Why It's WRONG | Required Action |
|-----------------|----------------|-----------------|
| "Snapshots are brittle" | Brittle = catches unintended changes. That's the point. | **Write snapshot tests** |
| "We test visually in browser" | Manual testing misses regressions. Automated is repeatable. | **Add snapshot tests** |
| "Only default state matters" | Error and loading states are user-facing. | **Test all states** |
| "Mobile is just smaller" | Layout changes at breakpoints. Test all viewports. | **Add responsive snapshots** |
| "This shadcn component is better" | sindarian-ui is PRIMARY. Don't duplicate. | **Check sindarian-ui first** |

---

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lerianstudio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
