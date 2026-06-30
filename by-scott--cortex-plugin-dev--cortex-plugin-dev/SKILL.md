---
name: context-budget
description: Manage context pressure during large codebase work without losing critical state. Use when this capability is needed.
metadata:
  author: by-scott
---

# Context Budget

${ARGS}

## Keep

Preserve active goals, constraints, changed files, unresolved decisions, verification state, and user preferences.

## Drop

Summarize repeated logs, dependency download noise, stale alternatives, and file contents already transformed into decisions.

## Retrieve

Use `symbol_search` and targeted CLI reads (`sed`, `grep`, `rg`, `git show`) instead of rereading whole files. Reconstruct context from durable project artifacts when possible.

## Handoff

Before compression or long transitions, write a concise brief with next action, blockers, and validation commands.

---
> Source: [by-scott/cortex-plugin-dev](https://github.com/by-scott/cortex-plugin-dev) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-30 -->
