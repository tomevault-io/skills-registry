---
name: agentic-loop
description: Review .ralph/config.json against the actual project and fix mismatches. Use when this capability is needed.
metadata:
  author: allierays
---

# Setup Review

Review `.ralph/config.json` and validate every setting against what actually exists in this project. Fix anything that's wrong.

## What to check

Read `.ralph/config.json`, then check each section against the real project files:

**1. commands.dev** — Does the dev command make sense?
- Check `package.json` scripts, `pyproject.toml`, `Makefile`, etc.
- Is the port in `urls.*` / `api.baseUrl` consistent with the dev command?

**2. commands.install** — Matches the actual package manager?
- `uv.lock` → `uv sync`, `poetry.lock` → `poetry install`, `pnpm-lock.yaml` → `pnpm install`, `yarn.lock` → `yarn install`, `package-lock.json` → `npm install`, `requirements.txt` → `pip install -r requirements.txt`

**3. checks.test / tests.\*** — Does the test runner exist?
- Check if `tests.directory` exists on disk
- Check if the test command actually works for this project (pytest, vitest, jest, go test, etc.)

**4. checks.lint** — Is the linter installed?
- Look for eslint, ruff, golangci-lint, clippy config files

**5. urls.\* / api.baseUrl** — Correct ports?
- Match ports to the dev command and any framework defaults

**6. paths.\* / tests.directory** — Do these directories exist?
- Check every path value against the filesystem

**7. playwright.\*** — Is Playwright actually set up?
- Check if `@playwright/test` is in package.json or playwright is pip-installed
- Does `playwright.testDir` exist?
- If Playwright isn't installed, set `playwright.enabled` to `false`

**8. migrations.\*** — Matches the actual migration tool?
- `prisma/` → prisma, `alembic/` or `alembic.ini` → alembic, `migrations/` + `manage.py` → Django, `priv/repo/migrations` → Ecto

**9. Python projects** — Correct package manager?
- `uv.lock` exists → commands should use `uv run`
- `poetry.lock` exists → commands should use `poetry run`
- Neither → plain `pip` / `python`

## How to fix

Edit `.ralph/config.json` directly to correct any mismatches.

## Output

Print a short summary. For each section, one line:
- What you checked
- Whether it was correct or what you changed

Keep it conversational — explain what each setting does in plain language so the user understands their config.

---
> Source: [allierays/agentic-loop](https://github.com/allierays/agentic-loop) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
