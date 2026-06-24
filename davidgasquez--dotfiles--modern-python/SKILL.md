---
name: modern-python
description: Use when writing, reviewing, or refactoring Python to ensure adherence to modern best practices (type syntax, `uv` instead of `python`, linting, formatting, etc.)
metadata:
  author: davidgasquez
---

- Never use `python`. Use `uv` instead of `python`
  - `uv add package-name`
  - `uv run script.py`
- Don't use `from __future__ import annotations`
- Use modern type syntax (`list[str], str | None`)
- Format with `uvx ruff format .`
- Lint with `uvx ruff check .` and `uvx ty check`
- Look before you leap (check conditions before acting)
- Don't keep backwards compatibility
- Declare variables close to use

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidgasquez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
