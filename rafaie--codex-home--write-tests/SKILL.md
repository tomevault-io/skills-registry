---
name: write-tests
description: Add or improve tests based on a feature spec's acceptance criteria. Use when this capability is needed.
metadata:
  author: rafaie
---

1) Read project `AGENTS.md`, `codex.toml` (if present), and the feature spec acceptance criteria.
2) Propose a test matrix (unit/integration/edge cases).
3) Implement tests under `tests/`.
4) Run the project test command (from `codex.toml` if present; otherwise `uv run pytest -q`).
5) Update the feature spec Test plan section with what was added.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rafaie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
