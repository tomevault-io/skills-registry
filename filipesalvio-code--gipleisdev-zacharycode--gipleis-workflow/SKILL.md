---
name: gipleis-workflow
description: Apply GipleisDev executive workflow (Architect → Execute → Audit → Polish). Use when planning, implementing, or auditing; when the user mentions Night Shift, Jules, agents, pipeline, or /1-/7 steps. Use when this capability is needed.
metadata:
  author: filipesalvio-code
---

# Gipleis workflow

## When to use this skill

- User is planning, implementing, or auditing work in GipleisDev.
- User mentions Night Shift, Jules, agent pipeline, or steps /1-brainstorm through /7-deliver.
- You need to decide whether to spec, code, audit, or polish.

## Workflow phases

1. **Architect (spec)** – Define the spec first. No code without a spec. Use `context/intent/` and PRD/tasks as source of truth.
2. **Execute (Jules)** – Build and test. Implement per spec; no placeholders. Write tests with implementation.
3. **Audit** – High-reasoning review (Agent 5/6: security + spec compliance). Hostile review; find bugs and logic gaps.
4. **Polish (Cursor)** – Visual/UX and final tweaks.

## Context and state

- **Source of truth:** FileSystem and `context/` (intent, decisions, knowledge). Not chat memory.
- **Active task state:** `.ralph/` (e.g. `RALPH_TASK.md`, `guardrails.md`). On failure or constraint violation, write a Sign to `.ralph/guardrails.md`.
- **Commits:** Prefix `ralph: [criterion] - summary`. One logical change per commit.

## Before a major build

- Reference `@context/intent/` (and relevant ADRs in `context/decisions/`) before implementing.
- If planning: no code generation; produce or update plan/spec first.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/filipesalvio-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
