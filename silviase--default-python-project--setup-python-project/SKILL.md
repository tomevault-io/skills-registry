---
name: setup-python-project
description: Bootstrap a new Python project using the OCREvaluation default layout and toolchain (uv, ruff, ty, pre-commit). Use when asked to scaffold a new Python repo, initialize standard folders, or set up uv/ruff/ty/pre-commit configs to match this repo. Use when this capability is needed.
metadata:
  author: silviase
---

# Setup Python Project

## Overview

Create the default Python project scaffold used in this repo: standard folders plus uv/ruff/ty/pre-commit configuration. Use the templates in `assets/` and then update placeholders.

## Inputs to confirm

- Project root path
- Project name for `pyproject.toml`
- Description and version (default `0.1.0`)
- Python version (default `3.12.7` with `>=3.12,<3.13`)
- Extra dependencies and whether to create `tests/`

## Workflow

1. Create the project directory (or confirm an empty root).
2. Create standard folders: `data`, `docs`, `output`, `sample`, `scripts`, `src`, optional `tests`.
3. Copy templates from `assets/` to the project root:
   - `.python-version`
   - `.gitignore`
   - `.pre-commit-config.yaml`
   - `pyproject.toml`
   - `README.md`
   - `main.py` (optional)
4. Edit `pyproject.toml`:
   - Set `project.name`, `description`, `version`.
   - Adjust `dependencies` (keep `pre-commit`, `ruff`, `pytest`, `ty` unless requested otherwise).
   - Update `requires-python` if the project targets a different Python.
5. Append `assets/uv-custom-indexes.toml` to `pyproject.toml` when custom uv indexes are required.
6. Run `uv sync` to generate `.venv` and `uv.lock`.
7. Install hooks: `uv run pre-commit install`; optionally run `uv run pre-commit run --all-files`.
8. Sanity-check tools: `uv run ruff format .`, `uv run ruff check .`, `uv run ty check src scripts`.

## Notes

- The pre-commit `unittest` hook prints a skip message when `tests/` is missing.
- Keep `.gitignore` aligned with the standard `data/` and `output/` directories.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/silviase) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
