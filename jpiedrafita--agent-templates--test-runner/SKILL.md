---
name: test-runner
description: Run tests in Fast/Full modes using repo-defined entrypoints (PROJECT.md/scripts) and report minimal, actionable results. Use when this capability is needed.
metadata:
  author: jpiedrafita
---

# test-runner

Purpose: Execute tests with minimal friction and report results clearly.

## Inputs
- PROJECT.md (how to run / quality gates)
- Repo scripts/entrypoints (Makefile/Taskfile/package scripts/CI)
- Optional: TASK-xxx Verification lines (if available)

## Modes
- Fast (default): run tests only, smallest set that gives quick feedback.
- Full: run the full suite as defined by the repo (may include integration/e2e).

## Command selection (prefer repo entrypoints)
Pick commands in this order:
1) PROJECT.md
2) Repo entrypoints: make/task/just/package scripts
3) Fallback by stack if no entrypoint exists (ask before running if unsure)

## Output
- Mode: Fast/Full
- Commands executed + exit code
- Short failure excerpt + pointer when

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpiedrafita) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
