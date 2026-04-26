---
name: gsd-quick
description: Quick single-phase GSD execution without full planning Use when this capability is needed.
metadata:
  author: erp-core-dev
---

# GSD Quick -- Single-Phase Execution

Execute a simple task in a fresh sub-agent context without full planning overhead.

## What To Do

1. Parse the task description
2. Spawn ONE fresh Task agent with the task
3. Verify the result
4. No STATE.md or ROADMAP.md needed (too lightweight)

Use this for tasks completable in a single agent session (< 30 tool calls).
For multi-phase tasks, use /gsd-plan instead.

## Arguments
- `<task-description>`: What to do (natural language)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erp-core-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
