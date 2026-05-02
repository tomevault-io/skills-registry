---
name: ts4
description: TS4 namespace for Netsnek e.U. TypeScript testing framework. Unit testing, snapshot testing, coverage reporting, and mutation testing. Use when this capability is needed.
metadata:
  author: openclaw
---

# Testing with TS4

TS4 is the Netsnek e.U. TypeScript testing framework. Run unit tests, snapshots, coverage reports, and mutation tests from a single CLI.

## Test Types

- **Unit** — Isolated function and module tests
- **Snapshot** — Output comparison for UI and serialization
- **Coverage** — Line, branch, and function metrics
- **Mutation** — Fault injection to verify test quality

## CLI Reference

| Flag | Effect |
|------|--------|
| `--run` | Execute the full test suite |
| `--coverage` | Produce coverage report (HTML + lcov) |
| `--status` | Show suite status, last run, pass/fail count |

## Walkthrough

```bash
# Run all tests
./scripts/test-runner.sh --run

# Generate coverage
./scripts/test-runner.sh --coverage

# Check status
./scripts/test-runner.sh --status
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
