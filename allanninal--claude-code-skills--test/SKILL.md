---
name: test
description: Auto-detect project type and run appropriate test suite. Use when running tests, checking test coverage, or validating code changes. Use when this capability is needed.
metadata:
  author: allanninal
---

# Universal Test Runner

Run tests for any project by auto-detecting the project type and test framework.

## Arguments
- `$0` (optional): Test type - `unit`, `integration`, `e2e`, `all` (default: `all`)
- `$1` (optional): Specific path or pattern to test

## Detection Logic

1. **Check for Python project** (pytest):
   - Look for `pytest.ini`, `pyproject.toml` with pytest config, or `conftest.py`
   - Check for `uv.lock` (use `uv run pytest`) or `requirements.txt` (use `pytest`)

2. **Check for JavaScript/TypeScript project**:
   - Look for `package.json` with test scripts
   - Detect test runner: Jest, Vitest, Playwright, Cypress

## Execution Steps

### For Python Projects (FastAPI, automation suites)

```bash
# Check if uv is used
if [ -f "uv.lock" ]; then
    uv run pytest $PATH -v --tb=short
else
    pytest $PATH -v --tb=short
fi
```

**Test type flags:**
- `unit`: `-m "unit or not (integration or e2e)"`
- `integration`: `-m "integration"`
- `e2e`: `-m "e2e"`
- `all`: No marker filter

**Parallel execution** (if pytest-xdist installed):
```bash
pytest -n auto $PATH
```

### For JavaScript/TypeScript Projects

**Jest/Vitest:**
```bash
npm test -- $PATH
# or for coverage
npm test -- --coverage
```

**Playwright E2E:**
```bash
npx playwright test $PATH
# or with UI
npx playwright test --ui
```

**Cypress:**
```bash
npx cypress run
# or interactive
npx cypress open
```

## Coverage Reports

After running tests, check for coverage:
- Python: Look for `htmlcov/index.html` or `.coverage` file
- JavaScript: Look for `coverage/lcov-report/index.html`

Report coverage percentage to the user.

## Common Patterns in User's Projects

| Project | Framework | Command |
|---------|-----------|---------|
| eruditiontx-services-mvp | pytest + asyncio | `uv run pytest -n auto` |
| mathmatterstx-services | pytest | `uv run pytest` |
| eruditiontx-client-mvp | Jest + Vitest | `npm test` |
| erudition-math-v2 | Jest + Playwright | `npm test` / `npm run test:e2e` |
| notaryo.ph | Jest + Playwright | `pnpm test` |
| bocs-turbo | Jest + Cypress | `pnpm test` |

## Output Format

```
Running tests for: [project-name]
Framework detected: [pytest/jest/playwright]
Test type: [unit/integration/e2e/all]

[Test output]

Summary:
- Passed: X
- Failed: Y
- Skipped: Z
- Coverage: XX%
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
