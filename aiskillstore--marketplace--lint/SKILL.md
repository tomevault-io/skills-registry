---
name: lint
description: How to lint and typecheck in this project. Load when implementing or verifying code. Use when this capability is needed.
metadata:
  author: aiskillstore
---

# Lint Skill

Project-specific linting and typechecking.

## Lint Commands

```bash
# Run linter
# TODO: Add your lint command
npx eslint {files}
# ruff check {files}
# golangci-lint run

# Auto-fix
npx eslint {files} --fix
# ruff check {files} --fix
```

## Typecheck Commands

```bash
# Run typecheck
# TODO: Add your typecheck command
npx tsc --noEmit
# mypy {files}
# go build ./...
```

## Common Issues

### Import Order
Run auto-fix to sort imports.

### Unused Variables
Remove or prefix with `_`.

### Type Errors
Check that types match expected signatures.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
