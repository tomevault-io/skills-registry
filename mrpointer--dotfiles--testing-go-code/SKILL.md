---
name: testing-go-code
description: Run Go unit tests, coverage reports, and benchmarks. Use when you need to run tests, check coverage, run benchmarks, or regenerate mocks after interface changes. Use when this capability is needed.
metadata:
  author: mrpointer
---

# Testing Go Code

All commands run from the Go module root (`installer/`).

## Unit Tests

```bash
task test                       # Run all tests with race detection
task test -- -run TestName      # Run specific test(s)
task test -- -short             # Skip integration tests
```

For test conventions (naming, assertions, table-driven patterns, mock usage), see the `writing-go-tests` skill.

## Coverage

```bash
task cov
```

Runs tests with coverage and opens an HTML report in the browser.

## Benchmarks

```bash
task bench
```

Runs all benchmarks with memory allocation stats.

## Combined Check

```bash
task check
```

Runs tests + lint in sequence. Use before committing.

## Mock Regeneration

```bash
mockery
```

Run from the module root with no arguments after adding or modifying interfaces. Configuration is in `.mockery.yml`. Never edit generated mock files (`*_mock.go`) manually.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mrpointer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
