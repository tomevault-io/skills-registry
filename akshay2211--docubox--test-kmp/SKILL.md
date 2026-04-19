---
name: test-kmp
description: Run the KMP test suite across platforms. Optionally pass a specific test class or method name to run a targeted test. Use when this capability is needed.
metadata:
  author: akshay2211
---

Run tests for the DocuBox KMP project.

If $ARGUMENTS is provided, treat it as a test filter (class or method name).

Steps:

1. Run common tests:
   ```
   ./gradlew :composeApp:allTests
   ```

2. If a specific test was requested, run with filter:
   ```
   ./gradlew :composeApp:allTests --tests "*$ARGUMENTS*"
   ```

3. If tests fail:
   - Read the test report for details
   - Show the failing test name, assertion, and expected vs actual values
   - Suggest a fix if the cause is clear

4. If all tests pass, confirm with a summary of tests run.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/akshay2211) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
