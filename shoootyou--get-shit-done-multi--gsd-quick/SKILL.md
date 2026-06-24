---
name: gsd-quick
description: Execute a quick task with GSD guarantees (atomic commits, state tracking) but skip optional agents Use when this capability is needed.
metadata:
  author: shoootyou
---
<objective>
Execute small, ad-hoc tasks with GSD guarantees (atomic commits, STATE.md tracking).

Quick mode is the same system with a shorter path:
- Spawns gsd-planner (quick mode) + gsd-executor(s)
- Quick tasks live in `.planning/quick/` separate from planned phases
- Updates STATE.md "Quick Tasks Completed" table (NOT ROADMAP.md)

**Default:** Skips research, plan-checker, verifier. Use when you know exactly what to do.

**`--full` flag:** Enables plan-checking (max 2 iterations) and post-execution verification. Use when you want quality guarantees without full milestone ceremony.
</objective>

<execution_context>
@{{PLATFORM_ROOT}}/get-shit-done/workflows/quick.md
</execution_context>

<context>
$ARGUMENTS

Context files are resolved inside the workflow (`init quick`) and delegated via `<files_to_read>` blocks.
</context>

<process>
Execute the quick workflow from @{{PLATFORM_ROOT}}/get-shit-done/workflows/quick.md end-to-end.
Preserve all workflow gates (validation, task description, planning, execution, state updates, commits).
</process>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shoootyou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
