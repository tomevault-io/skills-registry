---
name: playwright
description: E2E testing with Playwright Use when this capability is needed.
metadata:
  author: lewiatan
---

## TESTING

### Guidelines for E2E

#### PLAYWRIGHT

- Initialize configuration only with Chromium/Desktop Chrome browser
- Use browser contexts for isolating test environments
- Implement the Page Object Model for maintainable tests in ./e2e/page-objects
- Use `data-testid` attributes when introducing resilient test-oriented selectors
- When following `data-testid` convention, locate elements by `await page.getByTestId('selectorName')`
- Leverage API testing for backend validation
- Implement visual comparison with expect(page).toHaveScreenshot()
- Use the codegen tool for test recording
- Leverage trace viewer for debugging test failures
- Implement test hooks for setup and teardown
- Use expect assertions with specific matchers
- Leverage parallel execution for faster test runs
- Follow 'Arrange', 'Act', 'Assert' approach to test structure for simplicity and readability.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lewiatan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
