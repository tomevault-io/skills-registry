---
name: test
description: Run code quality checks and tests for the argo-trading project. Use this skill when: (1) User asks to test the code or run tests, (2) User asks to lint or check code quality, (3) User wants to verify code changes work correctly, (4) User says "/test" or asks to run the test suite. Use when this capability is needed.
metadata:
  author: rxtech-lab
---

# Test

Run linting and tests to ensure code quality.

## Workflow

1. Run linting with `make lint`
2. Run tests with `make test`
3. Report results and fix any issues found

## Commands

### Lint

```bash
make lint
```

Runs `golangci-lint run ./...` to check for code quality issues.

### Test

```bash
make test
```

Runs `go test ./...` to execute all tests.

### Full Quality Check

Run both commands sequentially:

```bash
make lint && make test
```

## Handling Failures

- **Lint failures**: Fix the reported issues before proceeding
- **Test failures**: Investigate failing tests, check error messages, and fix the underlying issues
- Report a summary of results to the user

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rxtech-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
