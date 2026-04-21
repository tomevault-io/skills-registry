---
name: task-breakdown
description: Break down an approved design into small, actionable tasks in specs/tasks.md (or feature-scoped). Produces TASK-xxx items with Implementation + Verification + traceability. Use when this capability is needed.
metadata:
  author: jpiedrafita
---

# task-breakdown

Purpose: Turn an approved design into a task list that is easy to implement and verify, with clear traceability.

## Inputs

- PROJECT.md (commands, repo conventions, quality gates)
- Requirements + design for the current scope:
  - Root: specs/requirements.md, specs/design.md
  - Feature: specs/features/<slug>/requirements.md, specs/features/<slug>/design.md

## Output

- A tasks file for the current scope:
  - Root: specs/tasks.md
  - Feature: specs/features/<slug>/tasks.md

## Rules

- Tasks MUST be implementable in small, reviewable diffs.
- One task = one clear objective.
- Prefer tasks that can be completed in one agent iteration (or S/M/L sizing if you don’t want time).
- Order tasks so they can be executed top-to-bottom: prerequisites → core functionality → integration → hardening → docs.
- Do not invent repo commands. If PROJECT.md is unclear, ask.
- Every task MUST include:
  - Implementation: lines (one per line)
  - Verification: lines (one per line)
  - Refs: pointing to REQ-xxx and/or Design: Section "..."

## Task template (copy/paste)

```markdown
- [ ] **TASK-001: <title>**
Short description.

Implementation:
<one line>
<one line>

Verification:
<one line command or check>
<one line command or check>

Refs: REQ-001 | Design: Section "<name>"
Deps: TASK-000 (optional) | Estimate: S/M/L
```

## How to break down the design

1) Identify deliverables from the design (components, interfaces, data shapes, migrations, tooling).
2) Create a “setup / scaffolding” task if needed (but only if the repo requires it).
3) Split by vertical slices when possible (feature-end-to-end), otherwise by layers.
4) Add explicit dependencies only when they truly block.
5) Ensure each task has a minimal, realistic Verification: (tests, linters, checks) that the repo supports.

## Verification guidance

- Prefer repo-defined commands from PROJECT.md or existing scripts (Makefile/Taskfile/package scripts).
- Keep verification “Fast” by default (unit tests / targeted checks).
- Add “Full” verification only for pre-merge tasks when the repo defines it.

## Optional: dependency summary

If there are many tasks (roughly > 8), add a short section at the end of the file:

## Dependencies (optional)
```markdown
- TASK-003 depends on TASK-001
- TASK-004 depends on TASK-002
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpiedrafita) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
