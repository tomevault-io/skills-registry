---
name: plan-progress-journal
description: Record implementation progress against responsibility-based plan tasks in this repository. Use when feature work, refactors, bug fixes, or test updates are completed and the matching task folder in plan/tasks/* must be updated with status changes and a dated work log. Use when this capability is needed.
metadata:
  author: nichori-115
---

# Plan Progress Journal

Use this workflow after implementing a change so the responsibility plan and history stay current.

## Workflow

1. Pick the responsibility area from `references/area-map.md`.
2. Run `scripts/record_progress.py` with area, summary, and files.
3. If a task is completed, pass `--task "..."` to mark the checklist item `[x]`.
4. Keep the summary concrete (what changed and why).

## Command

```bash
python3 .codex/skills/plan-progress-journal/scripts/record_progress.py \
  --area gameplay-engine \
  --status done \
  --summary "Split run scoring into dedicated engine file" \
  --files Sources/PenantSimLite/GameState+RunScoringEngine.swift Sources/PenantSimLite/PenantSimLite.swift \
  --task "Add inning-level run model"
```

## Outputs

- Updates `plan/tasks/<area>/TASKS.md` when `--task` is provided.
- Appends area history at `plan/tasks/<area>/HISTORY.md`.
- Appends central history at `plan/progress-log.md`.
- Triggers board refresh by running `improvement-board-sync/scripts/update_board.py`.

## Rules

- Do not use vague summaries such as "fix" or "update".
- Use relative repo paths in `--files`.
- Keep one execution per feature batch or bug-fix batch.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nichori-115) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
