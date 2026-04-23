---
name: refactoring-01-project-structure
description: Use when refactoring Python research code to improve project structure, modularity, and entrypoints.
metadata:
  author: silviase
---

# Refactoring 01: Project Structure

## Goal

Create a clear, modular structure where notebooks and scripts are thin and core logic lives in importable modules.

## Sequence

- Order: 01
- Previous: refactoring-00-preflight-assessment
- Next: refactoring-02-dependencies-env

## Workflow

- Identify entrypoints (notebooks, scripts, CLI) and the core logic they call.
  - Success: Entrypoints and their core modules are mapped.
- Move core logic into a `src/<package>/` module; keep entrypoints as orchestration only.
  - Success: Entrypoints import module functions and contain minimal glue code.
- Standardize imports and add `__init__.py` files for clean module boundaries.
  - Success: Imports resolve without relative import hacks.
- Separate concerns: data loading, preprocessing, modeling, evaluation, and visualization modules.
  - Success: Modules align to concerns with clear boundaries.
- Keep notebooks, but replace copied logic with imports from the module layer.
  - Success: Notebooks run using imports, not duplicated logic.

## Guardrails

- Preserve behavior; avoid algorithm changes unless requested.
- Keep diffs small and mechanical when moving code.
- Do not introduce new dependencies unless needed for the refactor.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/silviase) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
