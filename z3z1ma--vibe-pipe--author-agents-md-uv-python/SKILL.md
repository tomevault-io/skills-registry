---
name: author-agents-md-uv-python
description: Create/update AGENTS.md for a Python repo driven by uv (ruff/mypy/pytest), including single-test commands and editor rule discovery. Use when this capability is needed.
metadata:
  author: z3z1ma
---
<!-- BEGIN:compound:skill-managed -->
# Purpose
Create or improve `AGENTS.md` for a Python repo that uses `uv`.

# When To Use
- User asks to create/update `AGENTS.md`.
- Repo uses `uv` (e.g., `uv.lock`, CI uses `setup-uv`).

# Procedure
- Read existing `AGENTS.md` and preserve any tool-managed fences (do not edit inside).
- Add hard rules section if applicable:
  - Use `uv` only.
  - No Poetry (`poetry`, `poetry.lock`).
  - No Black if repo uses `ruff format`.
- Discover editor rules:
  - `.cursorrules`, `.cursor/rules/**`, `.github/copilot-instructions.md`.
- Discover commands from `pyproject.toml`, `.github/workflows/**`, `README.md`, `docs/**`, `.pre-commit-config.yaml`.
- Write UV-first commands:
  - Setup: `uv sync --dev`, then `uv pip install -e .`.
  - Format: `uv run ruff format ...`.
  - Lint: `uv run ruff check ...` (+ `--fix`).
  - Typecheck: `uv run mypy ...`.
  - Tests: include single file + single node id + markers.
  - Integration: include docker-compose steps and any helper scripts.
  - Docs: `uv run sphinx-build ...` if present.
- Keep the file concise (~150 lines) and copy/paste friendly.
<!-- END:compound:skill-managed -->

## Manual notes

_This section is preserved when the skill is updated. Put human notes, caveats, and exceptions here._

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/z3z1ma) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
