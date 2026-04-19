---
name: webapp-testing
description: Guide for testing web applications using Playwright. Use this when asked to create or run browser-based tests. Use when this capability is needed.
metadata:
  author: raffaferreira
---

# Web Application Testing with Playwright

This skill helps you create and run browser-based tests for web applications using Playwright.

## When to use this skill

Use this skill when you need to:

- Create new Playwright tests for web applications
- Debug failing browser tests
- Set up test infrastructure for a new project

## Creating tests

1. Review the [test template](./test-template.js) for the standard test structure
2. Identify the user flow to test
3. Create a new test file in the `tests/` directory
4. Use Playwright's locators to find elements (prefer role-based selectors)
5. Add assertions to verify expected behavior

## Running tests

To run tests locally:

```bash
npx playwright test
```

To debug tests:
```bash
npx playwright test --debug
```

## Best practices

- Use data-testid attributes for dynamic content
- Keep tests independent and atomic
- Use Page Object Model for complex pages
- Take screenshots on failure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/raffaferreira) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
