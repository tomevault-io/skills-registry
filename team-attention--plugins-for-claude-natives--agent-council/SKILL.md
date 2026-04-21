---
name: agent-council
description: - When you decide to use any Skill, **always** call `update_plan` immediately (before any potentially long-running shell/tool calls) so Codex’s Plan/To-do panel appears. Include **exactly one** `in_progress` item in that first plan. Use when this capability is needed.
metadata:
  author: team-attention
---
# Project Instructions (Codex)

## Plan / To-do UI (IMPORTANT)

- When you decide to use any Skill, **always** call `update_plan` immediately (before any potentially long-running shell/tool calls) so Codex’s Plan/To-do panel appears. Include **exactly one** `in_progress` item in that first plan.
- Keep the plan updated during execution (at most one `in_progress` step at a time).
- For `agent-council`: after the first `council.sh wait` (the non-blocking one), feed `.ui.codex.update_plan.plan` into `update_plan` right away, then repeat `wait → update_plan` until done.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/team-attention) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
