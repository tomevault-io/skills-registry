---
name: plan-audit
description: Audit an implementation against a plan (docs/codex-plans/*). Use when a user asks to check for gaps, logic errors, or missing tests relative to a plan or Work Items. Use when this capability is needed.
metadata:
  author: xiaolai
---

# Plan Audit

## Overview

Audits completed work against a plan (WIs + acceptance + tests) and reports gaps, logic errors, and missing coverage with file/line references.

## Workflow (Audit)

1) **Locate the plan**
   - Prefer `docs/codex-plans/<plan>.md` (local, not in repo).
   - If unclear, ask for the plan path or the WI list.

2) **Extract audit checklist**
   - For each WI, list:
     - Goal
     - Acceptance criteria
     - Tests required
     - Touched areas (files/symbols)

3) **Map implementation to WIs**
   - Use `git log`, `git show --stat`, `rg`, and file inspection.
   - Record which files/commits satisfy each WI.

4) **Gap & correctness analysis**
   - Check each acceptance item vs actual behavior.
   - Find logic errors, edge-case failures, and incomplete flows.
   - Validate test coverage against the “Tests (first)” section.
   - If the plan references specs, verify implementation matches those specs.
   - If the plan is missing acceptance/tests, record it as a plan-quality gap.

5) **Report findings**
   - Order by severity: Critical → High → Medium → Low.
   - Each finding must include:
     - WI reference
     - File path + line
     - Why it violates the plan
     - Expected behavior per plan

## Output Format (required)

- **Findings (ordered by severity)**  
  - `File:line` and WI reference
  - Impact and suggested fix
- **Plan Gaps Summary**  
  - WI‑### → missing/partial acceptance items
- **Test Coverage Gaps**  
  - Missing tests, broken tests, or “test not written”
- **Notes / Risks**  
  - Any assumptions or unverified areas
- **Evidence**
  - Cite concrete evidence for each finding (files/lines/commit IDs).

## Audit Rules

- Do **not** run tests here unless the user explicitly asks; this is an inspection pass.
- Be strict about spec drift: if behavior diverges from plan text, flag it.
- If you cannot locate the plan, stop and ask for it.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xiaolai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
