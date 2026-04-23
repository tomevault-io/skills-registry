---
name: uv-dependency-keeper
description: Manage Python dependencies with Astral uv (install, update, lockfiles) while keeping repos consistent. Use when this capability is needed.
metadata:
  author: codeswiftr
---

# uv Dependency Keeper

## When to Use
- Updating or adding Python packages in MVP repos.
- Regenerating lockfiles or syncing dependencies for collaborators.

## Workflow
1. Ensure uv is installed (`uv --version`). If not, install via `pip install uv` or follow Astral instructions.
2. To add a dependency: `uv add package==version` (from project root containing `pyproject.toml`).
3. To remove: `uv remove package`.
4. To sync environment: `uv sync`.
5. Regenerate lockfile as needed (`uv lock`).
6. Record changes in `pyproject.toml`/lockfiles and update documentation or commit notes.
7. Run tests/lints to confirm compatibility.

## Tips
- Pin exact versions for reproducibility.
- Update `.env.example` or docs if new env vars are required.
- For per-project venvs, rely on `uv`'s isolation (no manual `python -m venv` needed unless specified).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codeswiftr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
