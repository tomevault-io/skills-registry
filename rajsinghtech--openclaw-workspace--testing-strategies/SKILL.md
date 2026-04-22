---
name: testing-strategies
description: > Use when this capability is needed.
metadata:
  author: rajsinghtech
---

# Testing Strategies

## Routing

### Use This Skill When
- Identifying gaps in test coverage for a codebase
- Designing test cases for new features or bug fixes
- Choosing between unit, integration, and e2e testing approaches
- Validating that a PR includes adequate tests
- Someone asks "what tests do we need?" or "how should we test this?"

### Don't Use This Skill When
- Reviewing code for bugs or security issues → use **code-review**
- Debugging a test failure (the test exists but fails) → use **debug-troubleshooting**
- Validating YAML/JSON manifests → use **manifest-lint**
- Making architectural decisions about testability → use **architecture-design**
- Running infrastructure validation commands → use **manifest-lint** or **config-audit**

## Test Coverage Analysis

### Identify gaps

```bash
# Check existing tests
find . -name "*_test.go" -o -name "*.test.ts" -o -name "test_*.py" | wc -l

# Check test commands
grep -r "test" Makefile package.json 2>/dev/null
```

### Priority order for new tests

1. **Critical paths** — Auth, payments, data mutations
2. **Edge cases** — Nil inputs, empty collections, boundary values
3. **Error paths** — Network failures, invalid input, timeouts
4. **Integration points** — API boundaries, database queries, external services
5. **Regression** — Any bug that was found should get a test

## Test Design Principles

- **One assertion per test** — Tests should verify one behavior
- **Descriptive names** — `TestUserLogin_WithExpiredToken_ReturnsUnauthorized`
- **Arrange-Act-Assert** — Clear setup, action, verification
- **No test interdependence** — Each test runs independently
- **Test behavior, not implementation** — Don't test private methods directly

## Test Case Template

```markdown
### Test: <descriptive name>

**Scenario:** <what situation is being tested>
**Given:** <preconditions>
**When:** <action taken>
**Then:** <expected result>

**Edge cases:**
- <variation 1>
- <variation 2>
```

## Infrastructure Testing

For Kubernetes manifests:

```bash
# Syntax validation
jq . kustomization/openclaw.json > /dev/null
yq . kustomization/deployment.yaml > /dev/null

# Kustomize render
kustomize build kustomization/ > /dev/null

# Dry-run apply
kustomize build kustomization/ | kubectl apply --dry-run=client -f -
```

## Edge Cases

- **No existing tests:** Start with critical paths, don't try to add 100% coverage at once
- **Flaky tests:** Investigate timing, external dependencies, shared state — don't just re-run
- **Test infrastructure vs test code:** Infrastructure validation (kustomize build) is different from application unit tests — don't mix them

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rajsinghtech) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
