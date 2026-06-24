---
name: ringdev-property-testing
description: quick.Check used Use when this capability is needed.
metadata:
  author: lerianstudio
---

# Dev Property Testing (Gate 5)

## Overview

Ensure domain logic has **property-based tests** to verify invariants hold for all randomly generated inputs.

**Core principle:** Property tests verify universal truths about your domain. If "balance is never negative" is a rule, test it with thousands of random inputs.

<block_condition>
- No property functions = FAIL
- Any counterexample found = FAIL (fix and re-run)
- No quick.Check usage = FAIL
</block_condition>

## CRITICAL: Role Clarification

**This skill ORCHESTRATES. QA Analyst Agent (property mode) EXECUTES.**

| Who | Responsibility |
|-----|----------------|
| **This Skill** | Gather requirements, dispatch agent, track iterations |
| **QA Analyst Agent** | Write property tests, run quick.Check, report counterexamples |

---

## Standards Reference

**MANDATORY:** Load testing-property.md standards via WebFetch.

<fetch_required>
https://raw.githubusercontent.com/LerianStudio/ring/main/dev-team/docs/standards/golang/testing-property.md
</fetch_required>

---

## Step 1: Validate Input

```text
REQUIRED INPUT:
- unit_id: [task/subtask being tested]
- implementation_files: [files from Gate 0]
- language: [go]

OPTIONAL INPUT:
- domain_invariants: [list of invariants to verify]
- gate4_handoff: [full Gate 4 output]

if any REQUIRED input is missing:
  → STOP and report: "Missing required input: [field]"
```

## Step 2: Dispatch QA Analyst Agent (Property Mode)

```text
Task tool:
  subagent_type: "ring:qa-analyst"
  prompt: |
    **MODE:** PROPERTY-BASED TESTING (Gate 5)

    **Standards:** Load testing-property.md

    **Input:**
    - Unit ID: {unit_id}
    - Implementation Files: {implementation_files}
    - Language: {language}
    - Domain Invariants: {domain_invariants}

    **Requirements:**
    1. Identify domain invariants from code
    2. Create property functions (TestProperty_{Subject}_{Property} naming)
    3. Use testing/quick.Check for verification
    4. Report any counterexamples found

    **Output Sections Required:**
    - ## Property Testing Summary
    - ## Properties Report
    - ## Handoff to Next Gate
```

## Step 3: Evaluate Results

```text
Parse agent output:

if "Status: PASS" in output:
  → Gate 5 PASSED
  → Return success with metrics

if "Status: FAIL" in output:
  → Dispatch fix to implementation agent
  → Re-run property tests (max 3 iterations)
  → If still failing: ESCALATE to user
```

## Step 4: Generate Output

```text
## Property Testing Summary
**Status:** {PASS|FAIL}
**Properties Tested:** {count}
**Properties Passed:** {count}
**Counterexamples Found:** {count}

## Properties Report
| Property | Subject | Status |
|----------|---------|--------|
| {property_name} | {subject} | {PASS|FAIL} |

## Handoff to Next Gate
- Ready for Gate 6 (Integration Testing): {YES|NO}
- Iterations: {count}
```

---

## Common Properties to Test

| Domain | Example Properties |
|--------|-------------------|
| Money/Currency | Amount never negative, currency always valid, addition commutative |
| User/Account | Email always valid format, password meets policy, status transitions valid |
| Order/Transaction | Total equals sum of items, quantity always positive, state machine valid |
| Date/Time | Start before end, duration always positive, timezone valid |

---

## Severity Calibration

| Severity | Criteria | Examples |
|----------|----------|----------|
| **CRITICAL** | Counterexample found, invariant violated | quick.Check finds input that breaks property |
| **HIGH** | No property tests, missing quick.Check | Zero TestProperty_ functions, no testing/quick usage |
| **MEDIUM** | Incomplete properties, naming issues | Missing domain invariants, non-standard names |
| **LOW** | Documentation, optimization | Missing property descriptions, generator tuning |

Report all severities. CRITICAL = immediate fix (invariant broken). HIGH = fix before gate pass. MEDIUM = fix in iteration. LOW = document.

---

## Anti-Rationalization Table

| Rationalization | Why It's WRONG | Required Action |
|-----------------|----------------|-----------------|
| "Unit tests verify logic" | Unit tests verify SPECIFIC cases. Properties verify ALL cases. | **Write property tests** |
| "No domain invariants" | Every domain has rules. "ID is unique", "amount > 0", etc. | **Identify and test invariants** |
| "Too abstract" | Properties are concrete: "user.age >= 0 for all users". | **Write property tests** |
| "quick.Check is slow" | Milliseconds to find bugs that would take hours to discover. | **Write property tests** |

---

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lerianstudio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
