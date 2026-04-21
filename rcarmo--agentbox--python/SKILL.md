---
name: python-project-conventions
description: Project conventions for install, lint, test, format, and coverage via Make Use when this capability is needed.
metadata:
  author: rcarmo
---

# Skill: Python project conventions

## Goal
Provide a consistent Python dev experience (install/lint/test/format/coverage) via Make targets.

## Expected repo files
- `pyproject.toml` (preferred) or `requirements*.txt`
- Package code under `src/` (preferred) or top-level package dir
- Tests under `tests/`

## Make targets (recommended)
- `make install` → `python -m pip install -e .`
- `make install-dev` → install dev extras + ruff/pytest
- `make lint` → `ruff check ...`
- `make format` → `ruff format ...`
- `make test` → `python -m pytest`
- `make coverage` → `python -m pytest --cov=... --cov-report=term-missing`
- `make check` → `make lint && make coverage`

## Quality Bars
- End product should be installable from the final repo URL via `uv tool` or `pip`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rcarmo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
