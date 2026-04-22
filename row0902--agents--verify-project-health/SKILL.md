---
name: verify-project-health
description: Use when working with a quick workflow to verify linting, testing, and type safety.
metadata:
  author: row0902
---

# 🩺 Project Health Verification Skill

## Context
Before any `task_boundary` completion or `notify_user` call, code must be verified.

## Checklist

1.  **Type Check:**
    *   Mentally verify: "Are all arguments typed?" "Is `Any` avoided?"
    *   (Future) `uv run mypy src`

2.  **Linting (Strict):**
    *   Command: `uv run ruff check src`
    *   Action: Fix ALL errors. No ignore comments unless absolutely critical.

3.  **Formatting:**
    *   Command: `uv run ruff format src`

4.  **Testing:**
    *   Command: `uv run pytest`
    *   Action: If tests fail, FIX them. Do not comment them out.

## Troubleshooting Common Issues

*   **Ruff/Import Errors:** Check `pyproject.toml` configuration.
*   **Version Mismatch:** Run `uv sync` to ensure lockfile matches environment.
*   **Missing Dependencies:** Use `uv add [lib]` instead of `pip install`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/row0902) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
