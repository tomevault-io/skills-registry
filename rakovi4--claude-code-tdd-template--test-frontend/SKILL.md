---
name: test-frontend
description: Run frontend Vitest tests. Use when user wants to run frontend unit tests or mentions /test-frontend command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# Run Frontend Tests

## Action

All tests:
```bash
cd frontend && npx vitest run
```

With argument (test filter):
```bash
cd frontend && npx vitest run {argument}
```

## Examples

- `skill="test-frontend"` - run all frontend tests
- `skill="test-frontend", args="registration.logic"` - run registration logic tests
- `skill="test-frontend", args="registration.api"` - run registration API tests
- `skill="test-frontend", args="login"` - run all login-related tests

## Output

Report the test results from output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
