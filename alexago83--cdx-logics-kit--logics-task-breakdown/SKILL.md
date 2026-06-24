---
name: logics-task-breakdown
description: Break down a Logics backlog item into executable tasks. Use when Codex should turn `logics/backlog/*.md` into one or more `logics/tasks/*.md` with step-by-step plans, validation commands, and progress tracking. Use when this capability is needed.
metadata:
  author: alexago83
---

# Backlog → Tasks

Examples use `python ...` as the canonical cross-platform launcher.
If your environment only exposes `python3` or `py -3`, substitute that launcher.

## Do

- Start from the backlog item’s acceptance criteria; group work by deliverable.
- If the scope includes UI changes and no mockup exists yet, propose creating mockups (mobile + desktop) before writing tasks.
- Prefer 1–3 tasks per backlog item (split only if needed for parallelism or risk).
- For each task, include:
  - A minimal `# Plan` with checkboxes (implementation order).
  - A `# Validation` section using relevant commands (`npm run lint`, `npm run tests`, `npm run typecheck`, `npm run build`).
  - A short `# Report` updated as work progresses.
- Generate tasks:
  - `python logics/skills/logics.py flow promote backlog-to-task logics/backlog/<item_file>.md`
  - Or `python logics/skills/logics.py flow new task --title "..."`

## Avoid

- Vague plans (“do the thing”) without concrete steps.
- Missing validation commands for the area being changed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexago83) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
