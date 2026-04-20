---
name: lint
description: Run code linting and static analysis. Use to check code quality, find issues, or auto-fix style problems. Use when this capability is needed.
metadata:
  author: priyans-hu
---

# Lint - argus

Run code linting and static analysis.

## Command

```bash
make lint
```

## Description

Run linter

## On Failure

- Review each linting error
- Auto-fix when possible using `--fix` flag
- For remaining issues, fix manually
- Don't ignore warnings without good reason

## Success Criteria

- No linting errors
- Warnings are reviewed and addressed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/priyans-hu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
