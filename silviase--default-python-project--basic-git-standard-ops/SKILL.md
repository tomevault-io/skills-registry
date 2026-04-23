---
name: basic-git-standard-ops
description: Use for routine git operations in this repo (status, diff, checks, staging, commit, push) and GitHub actions via gh. Use when this capability is needed.
metadata:
  author: silviase
---

# Basic Git Standard Ops

## Overview

Use a consistent, low-risk git workflow for this repository.

## Workflow

1. Inspect state and diffs.
   - `git status -sb`
   - `git diff`
   - `git diff --staged`
2. Run checks before staging or committing.
   - Prefer: `uv run pre-commit run --all-files` (runs ruff format/check, prettier, ty, unittest).
   - Otherwise: `uv run ruff format .`, `uv run ruff check .`, `uv run ty check src scripts`, `uv run python -m unittest`
3. Stage intentionally.
   - Use `git add -p` or `git add <paths>` to keep commits scoped.
4. Commit with clear, scoped messages.
   - One logical change per commit; avoid mixing refactors, formatting, and behavior changes.
   - If functionality or milestones change, update `docs/progress.md`.
   - If updating planning docs, keep `docs/plan.md` focused on goals/targets with explicit success criteria, and keep `docs/tasks.md` as a deeper breakdown of plan items.
   - When creating or revising plans, define concrete success criteria in `docs/plan.md`.
5. Push with the appropriate upstream.
   - `git push` or `git push -u origin <branch>`
6. Use `gh` for GitHub operations beyond add/commit/push.
   - Issues: `gh issue create`, `gh issue view`, `gh issue close`
   - PRs: `gh pr create`, `gh pr view`, `gh pr checkout`, `gh pr merge`
   - Repo status: `gh repo view`

## Guardrails

- Do not commit secrets (Mapillary access tokens, API keys).
- Do not add generated content (`.venv`, `.ruff_cache`, or large output dirs).
- If pre-commit or formatters modify files, re-run checks and restage.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/silviase) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
