---
name: implement-issue-execplan
description: Implement a feature or fix by executing an issue card plus ExecPlan in this repo. Use when Codex is asked to implement work that must align with AGENTS.md and PLANS.md, keep the ExecPlan updated, and treat any referenced repo docs as authoritative. Requires ISSUE_PATH and EXECPLAN_PATH. Use when this capability is needed.
metadata:
  author: EEstevanell
---

# Implement Issue + ExecPlan

## Overview

Implement changes driven by an issue card and ExecPlan while keeping the plan updated, traceable, and aligned to AGENTS.md, PLANS.md, and any repo docs the issue references. We do not want over engineering, nor cluttering too much. We must follow the DRY principle and the best practices and guidelines.

## Inputs

- ISSUE_PATH: Path to the issue card file (required).
- EXECPLAN_PATH: Path to the ExecPlan file (required).

## Workflow (follow in order)

### 1) Inputs and preflight reading

- Require ISSUE_PATH and EXECPLAN_PATH; ask for them if missing or ambiguous.
- Read these files end-to-end, in order: `AGENTS.md`, `PLANS.md`, any repo docs referenced by the issue card, ISSUE_PATH, EXECPLAN_PATH.
- Treat referenced repo docs as authoritative for their scope; record conflicts in the ExecPlan Decision Log.

### 2) ExecPlan reconciliation (before code)

- Validate the ExecPlan includes every section required by `PLANS.md`.
- Ensure each plan step is deterministic, file-scoped, and includes validation criteria.
- Update the ExecPlan in place if anything is missing or stale; include assumptions, risks, and dependencies.

### 3) Implementation loop (repeat per step)

- Execute only one planned step (or a tight subset) at a time.
- Keep diffs minimal, follow existing patterns, and avoid new abstractions unless they clearly reduce duplication.
- Update the ExecPlan after each step.
- Record progress: what changed and which steps are complete/in progress.
- Record the decision log: why choices were made, with references to AGENTS/PLANS/Dingus docs or issue text.
- Record validation: how to verify the step and expected outcome.

### 4) Validation and closure

- Run every validation listed in the ExecPlan.
- Add missing validations discovered during implementation and record results. 
- Ensure acceptance criteria from the issue are explicitly satisfied and verifiable.

## Output requirements

- Summarize what changed (short).
- List exact file paths touched and why.
- Provide validation evidence (commands run plus result or expected output).
- Confirm the ExecPlan is complete and up to date.

## Example triggers

- "Implement ISSUE-123 with ExecPlan at docs/ExecPlans/ISSUE-123.md"
- "Ship the Dingus sync changes; follow the ExecPlan and keep it updated"
- "Use implement-issue-execplan with ISSUE_PATH=docs/Issues/ISSUE-123.md and EXECPLAN_PATH=docs/ExecPlans/ISSUE-123.md"

---
> Source: [EEstevanell/codex-planning-kit](https://github.com/EEstevanell/codex-planning-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
