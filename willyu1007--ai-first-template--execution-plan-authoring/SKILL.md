---
name: execution-plan-authoring
description: How to author execution-ready plans. Keywords: plan, authoring, execution. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Execution Plan Authoring

This document is an entrypoint for execution plan authoring. The step-by-step workflow is in
`/.system/skills/ssot/repo/execution-plans/execution-plan-authoring-steps/SKILL.md`.

---

## Purpose & Scope

Use this workflow when the task is non-trivial, for example:
- Multi-step work with dependencies (typically 3+ steps)
- Cross-module or cross-scope changes
- Risky changes (security, production adjacency, registries, generated artifacts)
- Work that needs human checkpoints or approvals
- Work likely to span multiple sessions

Do NOT use this workflow for trivial work:
- Single-file/single-step changes that can be completed safely in one pass
- Changes that do not require approvals, coordination, or durable task state

For trivial work, use a micro plan (3-5 bullets in chat) and proceed without creating full workdocs.

---

## Inputs & Preconditions

Inputs:
- Goal statement (what success looks like)
- Working scope (module vs integration vs ops vs knowledge vs system)
- Constraints (applicable `AGENTS.md`, safety rules, approvals, forbidden areas)
- Known risks and unknowns

Preconditions:
- Read the most local applicable `AGENTS.md` for the working scope.
- Create or open the task's workdocs folder (scenario-local):
  - Recommended: `workdocs/active/T-YYYYMMDD-slug/` with `plan.md`, `context.md`, `tasks.md`.

---

## Steps

See `/.system/skills/ssot/repo/execution-plans/execution-plan-authoring-steps/SKILL.md`.

---

## Related

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
