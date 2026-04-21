---
name: uv-init
description: Bootstrap a new Python project with uv, ruff, pytest Use when this capability is needed.
metadata:
  author: anotherjson
---

Bootstrap a new Python project with modern tooling.

Steps:
1. Run `uv init $ARGUMENTS` (project name from arguments, or ask)
2. Add dev dependencies: `uv add --dev ruff pytest mypy`
3. Configure pyproject.toml with ruff rules and pytest config
4. Create src layout with __init__.py
5. Create tests/ directory with conftest.py
6. Create .gitignore if not present
7. Initialize git repo if not already one
8. Print summary of created structure

Follow functional programming conventions: pure functions, type hints, no classes unless necessary.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anotherjson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
