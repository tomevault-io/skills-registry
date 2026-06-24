---
name: align-issue-card
description: Validate an issue card + ExecPlan against AGENTS.md and PLANS.md and propose junior-proof edits (including exact patch-style changes). Requires ISSUE_PATH and EXECPLAN_PATH. Use when this capability is needed.
metadata:
  author: eestevanell
---

# Align Issue Card + ExecPlan

## Purpose

Ensure an issue card and its ExecPlan comply with `AGENTS.md` and `PLANS.md` so the work is safe to hand to a junior developer, with no ambiguity or underspecified steps. Optimize for correctness, maintainability, and low blast radius. Avoid overengineering and speculative abstractions.

## Inputs (required)

- `ISSUE_PATH`: path to the issue card Markdown file.
- `EXECPLAN_PATH`: path to the ExecPlan Markdown file.

If either is missing or ambiguous, ask for them before proceeding.

## Procedure

1. Read `AGENTS.md` and `PLANS.md` from the repo root first.
2. Read `ISSUE_PATH` and `EXECPLAN_PATH` fully.
3. Produce output using the template at `assets/align_issue_card_rubric.md` (or mimic it closely):
   - Compliance matrix mapped to the exact `AGENTS.md` / `PLANS.md` requirements.
   - Gap list with severity and a junior-proofness audit (undefined terms, vague steps, missing file paths, missing validation, unclear acceptance, unresolved decisions).
   - Exact proposed edits (patch-style or "replace this section with ...").
   - A junior-proof Acceptance Criteria + Verification Plan with explicit commands and observable outcomes.
4. Resolve ambiguities by inspecting the repo (do not ask the user unless blocked).

## Notes

- Prefer making a decision (and documenting it) over leaving open questions, unless the change is truly blocked.
- If the issue card and ExecPlan disagree, make them consistent and record the rationale.

## Output format
- Summary (include junior-proof gaps)
- Compliance matrix (Requirement -> Evidence -> Status -> Fix)
- Proposed edits
- Final "ready to implement" checklist

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eestevanell) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
