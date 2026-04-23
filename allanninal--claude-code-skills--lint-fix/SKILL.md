---
name: lint-fix
description: Auto-format code and fix linting issues across Python and JavaScript/TypeScript projects. Use when cleaning up code, fixing style issues, or preparing for commit. Use when this capability is needed.
metadata:
  author: allanninal
---

# Universal Linter & Formatter

Auto-detect project type and run appropriate formatters and linters with auto-fix.

## Arguments
- `$ARGUMENTS`: Specific path or file to lint (optional - uses current directory)

## Detection & Tools

### Python Projects

**Formatters:**
- `black` - Code formatter
- `isort` - Import sorter

**Linters:**
- `flake8` - Style checker
- `mypy` - Type checker
- `ruff` - Fast linter (replaces flake8, isort)

**Execution Order:**
```bash
# 1. Sort imports
uv run isort $PATH --profile black
# or with ruff
uv run ruff check --select I --fix $PATH

# 2. Format code
uv run black $PATH

# 3. Lint (auto-fix what's possible)
uv run ruff check --fix $PATH
# or
uv run flake8 $PATH

# 4. Type check (no auto-fix)
uv run mypy $PATH
```

### JavaScript/TypeScript Projects

**Formatters:**
- `prettier` - Code formatter

**Linters:**
- `eslint` - Linter with auto-fix

**Execution Order:**
```bash
# 1. Format with Prettier
npx prettier --write $PATH
# or with pnpm
pnpm prettier --write $PATH

# 2. Lint with ESLint (auto-fix)
npx eslint --fix $PATH
# or
pnpm eslint --fix $PATH

# 3. Type check (TypeScript only)
npx tsc --noEmit
```

## Project-Specific Configurations

| Project | Tools | Config Files |
|---------|-------|--------------|
| eruditiontx-services-mvp | black, flake8, cleanpy | `pyproject.toml`, `.flake8` |
| mathmatterstx-services | black, flake8 | `pyproject.toml` |
| eruditiontx-client-mvp | ESLint, Prettier | `.eslintrc`, `.prettierrc` |
| erudition-math-v2 | ESLint, Prettier | `eslint.config.mjs` |
| notaryo.ph | ESLint, Prettier | `eslint.config.mjs`, `prettier.config.js` |
| bocs-turbo | ESLint, Prettier | Workspace configs |

## Pre-commit Integration

Many projects use pre-commit hooks. Run manually:

```bash
# Python projects
pre-commit run --all-files

# This runs all configured hooks:
# - black
# - flake8
# - isort
# - cleanpy (remove __pycache__)
```

## Staged Files Only

For commit preparation, lint only staged files:

```bash
# Python
git diff --cached --name-only --diff-filter=ACMR | grep '\.py$' | xargs black
git diff --cached --name-only --diff-filter=ACMR | grep '\.py$' | xargs flake8

# JavaScript/TypeScript
git diff --cached --name-only --diff-filter=ACMR | grep -E '\.(js|jsx|ts|tsx)$' | xargs npx prettier --write
git diff --cached --name-only --diff-filter=ACMR | grep -E '\.(js|jsx|ts|tsx)$' | xargs npx eslint --fix
```

## Output Format

```
Lint & Format: [project-name]
Path: [path or .]
Project Type: [Python/JavaScript/TypeScript]

1. Formatting...
   [Formatter output]
   Files formatted: X

2. Linting...
   [Linter output]
   Issues fixed: Y
   Issues remaining: Z

3. Type Checking...
   [Type checker output]
   Type errors: N

Summary:
  Formatted: X files
  Auto-fixed: Y issues
  Manual fixes needed: Z issues
  Type errors: N
```

## Common Issues & Fixes

### Python
- **Line too long**: Black handles this, or add `# noqa: E501`
- **Import order**: `isort` or `ruff check --select I --fix`
- **Unused imports**: `ruff check --select F401 --fix`

### JavaScript/TypeScript
- **Unused variables**: ESLint `--fix` with `no-unused-vars`
- **Missing semicolons**: Prettier handles this
- **Inconsistent quotes**: Prettier handles this

## Skip Linting

For files that should be skipped:

```python
# Python: Add to file
# flake8: noqa
# type: ignore
```

```javascript
// JavaScript: Add to file
/* eslint-disable */
// prettier-ignore
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
