---
name: backend-formatting
description: How to run Ruff to format Python files Use when this capability is needed.
metadata:
  author: jpmolinamatute
---

# Python Formatting

We use ruff to format all Python files. We also use ./backend/pyproject.toml to configure Ruff.
There are two ways to run formatting

1. Manually:

   ```bash
   cd ./backend
   uv run ruff format --config ./pyproject.toml
   ```

2. Via script (run from project root), this will also run lint, type annotation check and tests:

   ```bash
   ./scripts/linting.bash --backend
   ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpmolinamatute) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
