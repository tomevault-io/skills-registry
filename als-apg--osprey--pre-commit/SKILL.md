---
name: osprey-pre-commit
description: > Use when this capability is needed.
metadata:
  author: als-apg
---

# Pre-Commit Validation

This skill helps you validate your code changes before committing.

## Instructions

Follow the validation workflow in [instructions.md](../../../tasks/pre-commit/instructions.md).

## Quick Command Reference

### Quick Check (Before Commits)

```bash
# Auto-fix code style
ruff check src/ tests/ --fix --quiet || true
ruff format src/ tests/ --quiet

# Run fast tests
pytest tests/ --ignore=tests/e2e -x --tb=line -q
```

Or use the script:
```bash
./scripts/quick_check.sh
```

### Full CI Check (Before Pushing)

```bash
./scripts/ci_check.sh
```

## Workflow

1. **Auto-fix** formatting and simple lint issues
2. **Run tests** to catch regressions
3. **Fix failures** if any tests fail
4. **Re-run** until all checks pass
5. **Commit** with confidence

## When to Use Each Check

| Situation | Command |
|-----------|---------|
| Ready to commit | `./scripts/quick_check.sh` |
| Ready to push | `./scripts/ci_check.sh` |
| Creating PR | `./scripts/premerge_check.sh main` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/als-apg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
