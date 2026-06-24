---
name: sv-issue-tracking
description: Use sv task to manage work items and keep .tasks/ in sync with code changes. Use when this capability is needed.
metadata:
  author: mbtz
---

Use `sv task` for issue tracking in this repo.

- List ready work with `sv task ready` or `sv task list --status open`.
- View details with `sv task show <id>`.
- Mark work in progress with `sv task start <id>`.
- Close work with `sv task close <id>`.
- Tasks live in `.tasks/tasks.jsonl` (tracked) and should be committed with related code changes.

Reference: `references/sv-task-quickref.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mbtz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
