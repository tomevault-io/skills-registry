---
name: quality-check
description: Run comprehensive quality checks including linting, type checking, and tests. Use before committing or when validating code changes. Use when this capability is needed.
metadata:
  author: dcrepaircenter
---

# Quality Check Skill

Execute comprehensive quality checks on the codebase.

## Commands

```bash
# Python linting
make lint          # ruff check (no auto-fix)
make format        # ruff format (auto-fix)

# Type checking
make typecheck     # mypy

# Rust checks
make rust-check    # cargo clippy

# Run tests
make test          # pytest full suite
make test-unit     # unit tests only
make test-cov      # with coverage report

# All checks at once
make check         # lint + typecheck + test
```

## Check Results Interpretation

### Ruff Errors
- `E` - pycodestyle errors
- `W` - pycodestyle warnings
- `F` - Pyflakes
- `I` - isort
- `B` - flake8-bugbear
- `UP` - pyupgrade

### Mypy Errors
- Missing type annotations
- Type mismatches
- Import errors

### Clippy Warnings
- `clippy::unwrap_used` - Use `?` or `expect()`
- `clippy::todo` - Remove before commit
- `clippy::dbg_macro` - Remove debug macros

## Focus Areas

- Type annotation completeness
- Import organization
- Unused variables/imports
- Code complexity
- Test coverage gaps

## Workflow

1. Run `make check` to see all issues
2. Report errors with file:line references
3. Suggest specific fixes
4. Prioritize by severity (errors > warnings > style)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dcrepaircenter) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
