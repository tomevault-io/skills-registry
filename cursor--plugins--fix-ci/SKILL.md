---
name: fix-ci
description: Find failing CI jobs, inspect logs, and apply focused fixes Use when this capability is needed.
metadata:
  author: cursor
---

# Fix CI

## Trigger

Branch CI is failing and needs a fast, iterative path to green checks.

## Workflow

1. Identify the latest run for the current branch.
2. Inspect failed jobs and extract the first actionable error.
3. Apply the smallest safe fix.
4. Re-run CI and repeat until green.

## Guardrails

- Fix one actionable failure at a time.
- Prefer minimal, low-risk changes before broader refactors.

## Output

- Primary failing job and root error
- Fixes applied in iteration order
- Current CI status and next action

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cursor) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
