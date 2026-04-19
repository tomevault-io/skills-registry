---
name: test
description: Run unit tests with coverage Use when this capability is needed.
metadata:
  author: leanethereum
---

# /test - Run Unit Tests

Run pytest unit tests.

## Default

```bash
uvx tox -e pytest
```

## Options

Pass additional arguments after `--`:

- `/test -- -v` - Verbose output
- `/test -- -k "test_serialize"` - Run matching tests
- `/test -- tests/lean_spec/subspecs/ssz/` - Run specific test directory

## Examples

- `/test` - Run all unit tests
- `/test -- --cov` - Run with coverage report

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/leanethereum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
