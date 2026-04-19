---
name: test
description: Run the pytest test suite Use when this capability is needed.
metadata:
  author: sjsu-cs-systems-group
---

# Run Tests

Run the project's test suite using pytest.

## Default behavior (no arguments)

Run all tests:
```bash
pytest tests/ -v
```

## With arguments

If `$ARGUMENTS` is provided, use it as a test filter:
```bash
pytest tests/ -v -k "$ARGUMENTS"
```

Examples:
- `/test` - run all tests
- `/test codeval` - run tests matching "codeval"
- `/test "basic or compile"` - run tests matching "basic" or "compile"

## On failure

If tests fail, analyze the output and suggest fixes. Do not automatically modify code unless the user asks.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sjsu-cs-systems-group) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
