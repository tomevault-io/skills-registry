---
name: uv-manage
description: Manages Python project dependencies and pyproject.toml using the uv tool. Activate when the user asks to add/remove packages, sync environments, or initialize Python projects.
metadata:
  author: pluto-atom-4
---

# Skill Instructions

When managing Python projects, strictly use `uv` instead of `pip`, `poetry`, or `conda`.

## Core Commands to Use

- **Initialization:** Use `uv init` for new projects.
- **Adding Dependencies:** Use `uv add <package>` for main dependencies and `uv add --dev <package>` for development dependencies.
- **Removing Dependencies:** Use `uv remove <package>`.
- **Locking:** If `pyproject.toml` is manually edited, run `uv lock` to update the lockfile.
- **Execution:** Use `uv run <command>` to run scripts within the project's virtual environment.
- **Environment Setup:** Use `uv python install` to manage Python versions.

## Guidelines

1. Always check for an existing `pyproject.toml` before running commands.
2. If `uv` is not installed on the system, notify the user but do not attempt to use `pip`.
3. Prefer `uvx` for one-off tool executions (e.g., `uvx ruff check .`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluto-atom-4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
