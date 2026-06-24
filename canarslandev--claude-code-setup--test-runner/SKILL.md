---
name: test-runner
description: Run tests intelligently and fix failures Use when this capability is needed.
metadata:
  author: canarslandev
---

# Test Runner Skill

Run the project's test suite, analyze results, and optionally fix failing tests.

## Usage

```
/test-runner                    # Run all tests
/test-runner src/auth/          # Run tests for a specific directory
/test-runner --fix              # Run tests and fix failures
/test-runner --coverage         # Run tests with coverage report
```

## Steps

1. **Detect the test framework**
   Check the project for:
   - `jest.config.*` or `"jest"` in package.json → Jest
   - `vitest.config.*` → Vitest
   - `pytest.ini`, `pyproject.toml` [tool.pytest] → pytest
   - `go test` → Go testing
   - `cargo test` → Rust testing
   - `.rspec` → RSpec
   - `phpunit.xml` → PHPUnit

2. **Build the test command**
   - All tests: use the project's test script (`npm test`, `pytest`, etc.)
   - Specific path: pass the path to the test runner
   - Coverage: add the appropriate coverage flag
   - Use verbose output when available for better diagnostics

3. **Run the tests**
   Execute the test command and capture the output.

4. **Analyze the results**
   Parse the output to identify:
   - Total tests, passed, failed, skipped
   - Which specific tests failed
   - Error messages and stack traces
   - Coverage percentages (if requested)

5. **Report results**
   ```
   ## Test Results

   ✓ X passed | ✗ X failed | ○ X skipped | Total: X

   ### Failures
   - test_name (file:line) — Error: description

   ### Coverage (if requested)
   - Statements: X%
   - Branches: X%
   - Functions: X%
   - Lines: X%
   ```

6. **Fix failures (if --fix flag)**
   For each failing test:
   - Read the test file and the source file it tests
   - Determine if the issue is in the test or the source code
   - If the test is wrong: fix the test
   - If the source is wrong: fix the source
   - Re-run to verify the fix
   - Repeat until all tests pass

## Rules

- Always run the existing test command — don't invent your own
- When fixing, prefer fixing source code bugs over changing test expectations
- Never delete or skip failing tests to make the suite pass
- If a test is genuinely outdated, explain why before updating it
- Show the full error output for failures so the user can review

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canarslandev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
