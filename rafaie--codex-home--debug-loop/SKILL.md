---
name: debug-loop
description: Debug by reproducing, minimizing, writing regression tests, fixing, and documenting. Use when this capability is needed.
metadata:
  author: rafaie
---

1) Reproduce the failure (exact command, inputs, environment).
2) Minimize to the smallest failing case.
3) Add a regression test that fails before the fix.
4) Fix the root cause.
5) Re-run the project test command (`codex.toml` `test` if present; otherwise `uv run pytest -q`).
6) Update the feature spec Debug log with repro, root cause, fix, and regression test.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rafaie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
