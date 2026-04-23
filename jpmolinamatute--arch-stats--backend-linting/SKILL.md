---
name: backend-linting
description: How to run Ruff to lint python files Use when this capability is needed.
metadata:
  author: jpmolinamatute
---

# Python Linting

We use ruff to lint all Python files. We also use ./backend/pyproject.toml to configure Ruff.
There are two ways to run linting

1. Manually:

   ```bash
   cd ./backend
   uv run ruff check --fix --config ./pyproject.toml
   ```

2. Via script (run from project root), this will also run formatting, type annotation check and tests:

   ```bash
   ./scripts/linting.bash --backend
   ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpmolinamatute) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
