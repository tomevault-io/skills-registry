---
name: refactoring-02-dependencies-env
description: Use when standardizing Python research code dependencies, environment setup, and lockfiles.
metadata:
  author: silviase
---

# Refactoring 02: Dependencies and Environment

## Goal

Make the environment reproducible by declaring dependencies, pinning versions, and documenting setup.

## Sequence

- Order: 02
- Previous: refactoring-01-project-structure
- Next: refactoring-03-config-reproducibility

## Workflow

- Inventory imports and hidden runtime assumptions (system binaries, CUDA, external tools).
  - Success: Dependencies and external requirements are listed.
- Consolidate dependencies into `pyproject.toml` (or the repo standard).
  - Success: Declared deps reflect actual imports.
- Use `uv` for add/remove/sync/lock operations in this repo.
  - Success: Dependency changes are made via `uv` commands.
- Pin the Python version and ensure a lockfile exists and is current.
  - Success: Python version and lockfile are present and up to date.
- Document setup steps and required system packages in `README.md`.
  - Success: README includes reproducible setup instructions.

## Guardrails

- Prefer existing dependency management conventions in the repo.
- Avoid over-pinning transitive deps unless reproducibility demands it.
- Keep environment changes isolated from code refactors.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/silviase) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
