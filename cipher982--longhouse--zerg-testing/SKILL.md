---
name: zerg-testing
description: Zerg testing workflow (unit + E2E). Use when running or debugging tests. Use when this capability is needed.
metadata:
  author: cipher982
---

# Zerg Testing

## Rules
- Always use Make targets. Never run pytest/bun/playwright directly.

## Core Commands
```bash
make test                # unit tests
make test-e2e-core       # core E2E (must pass 100%)
make test-e2e            # full E2E (retries ok)
make test-full           # unit + full E2E + visual checks
make test-e2e-single TEST=tests/<spec>.ts
make test-e2e-errors     # show last E2E errors
make test-e2e-verbose    # full output for debugging
```

## Debugging Flow
1) `make test-e2e-errors`
2) `make test-e2e-single TEST=tests/<spec>.ts`
3) `make test-e2e-verbose`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cipher982) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
