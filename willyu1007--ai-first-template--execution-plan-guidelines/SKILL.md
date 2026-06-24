---
name: execution-plan-guidelines
description: Guidelines for creating execution plans. Keywords: plan, guidelines, execution. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Execution Plans

This skill helps you produce **execution-ready plans** and maintain **workdocs** so complex work can be resumed, reviewed, and audited across sessions without relying on provider-specific features.

---

## Purpose

- Turn a vague request into an executable plan with explicit outputs, checks, dependencies, and gates.
- Keep scenario-local task state in workdocs (`plan.md`, `context.md`, `tasks.md`) so progress survives session boundaries.
- Reduce risk on non-trivial changes by making validation and human approvals explicit.

---

## When to use

Use this skill when:
- The task is multi-step (dependencies) or cross-scope (modules + system + ops + knowledge).
- The task requires approval gates (security, registries, production-adjacent config).
- You need durable handoff notes (context resets, multi-session work).

Do not use this skill for trivial one-shot edits; use a short micro plan in chat.

---

## Core concepts (repository-native)

### Workdocs are scenario-local state

- Workdocs live under `workdocs/` directories (typically `workdocs/active/T-YYYYMMDD-slug/`).
- Workdocs are the source of truth for 鈥渨hat was planned, what changed, and what鈥檚 next鈥?

### Execution plans must be verifiable

- Each planned unit of work should name concrete `outputs` and `checks`.
- Add `gates` whenever progress requires human input/approval.

---

## Minimal workflow

1. Create/open `workdocs/active/T-YYYYMMDD-slug/`.
2. Author an execution-ready `plan.md` (YAML todos + narrative).
3. Maintain `context.md` and `tasks.md` as you work.
4. Before ending a session, run a handoff update pass to capture the exact state and next steps.

---

## Related

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
