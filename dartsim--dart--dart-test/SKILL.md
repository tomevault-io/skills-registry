---
name: dart-test
description: DART testing patterns - unit tests, integration tests, CI validation Use when this capability is needed.
metadata:
  author: dartsim
---

# DART Testing

Load this skill when writing or debugging tests.

## Quick Commands

```bash
pixi run test         # Quick test run
pixi run test-all     # Full validation
ctest -R <pattern>    # Run specific tests
ctest -V              # Verbose output
```

## Full Documentation

For complete testing guide: `docs/onboarding/testing.md`

For CI/CD troubleshooting: `docs/onboarding/ci-cd.md`

## Test Organization

- Unit tests: `tests/unit/`
- Integration tests: `tests/integration/`
- Regression tests: Near the code they test

## Writing Tests

1. Follow existing patterns in the test directory
2. Use GoogleTest framework
3. Name tests descriptively: `TEST(ClassName, MethodName_Condition_ExpectedResult)`

## CI Validation

Before submitting PR:

```bash
pixi run lint         # Must pass
pixi run test-all     # Must pass
```

## Debugging Test Failures

```bash
# Run single test with verbose output
ctest -R TestName -V

# Get CI logs
gh run view <RUN_ID> --log-failed
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dartsim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
