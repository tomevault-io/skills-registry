---
name: playwright-e2e
description: This skill should be used when the user asks to "write playwright test", "e2e test", "playwright locator", "test isolation", "page object", or needs guidance on Playwright E2E testing patterns, locator strategies, test structure, or debugging flaky tests. Use when this capability is needed.
metadata:
  author: nexus-labs-automation
---

# Playwright E2E Testing

Write tests that mimic user behavior, not implementation details.

## Core Principles

1. **Test user-visible behavior** - Use role-based locators, assert visible outcomes
2. **Isolate every test** - Fresh context, own data, no shared state
3. **Use web-first assertions** - `expect(locator).toBeVisible()` auto-waits
4. **Never test third parties** - Mock OAuth, external APIs, analytics
5. **Prefer API setup over UI** - Seed data via API, test UI separately

## Locator Priority (Use in Order)

| Priority | Method | Example |
|----------|--------|---------|
| 1 | `getByRole()` | `getByRole('button', { name: 'Submit' })` |
| 2 | `getByLabel()` | `getByLabel('Email address')` |
| 3 | `getByText()` | `getByText('Welcome back')` |
| 4 | `getByPlaceholder()` | `getByPlaceholder('Search...')` |
| 5 | `getByTestId()` | `getByTestId('submit-btn')` |
| 6 | CSS/XPath | **AVOID** - fragile, breaks with styling |

## Critical Anti-Patterns

- **Never `waitForTimeout()`** - Use web-first assertions instead
- **Never CSS class selectors** - `.MuiButton-root` breaks on updates
- **Never XPath** - Hard to read, brittle
- **Never test dependencies** - Tests affect each other
- **Never skip isolation** - Parallel execution will fail

## Quick Patterns

```typescript
// ✅ Good: Role-based, auto-waiting
await expect(page.getByRole('button', { name: 'Save' })).toBeEnabled();
await page.getByRole('button', { name: 'Save' }).click();

// ❌ Bad: CSS selector, manual wait
await page.waitForTimeout(2000);
await page.locator('.save-btn').click();
```

## References

For detailed patterns, see:
- `references/locators.md` - Chaining, filtering, nth-child
- `references/page-objects.md` - POM + fixtures pattern
- `references/auth.md` - storageState, multi-role
- `references/network.md` - Mocking, HAR recording
- `references/ci-cd.md` - GitHub Actions, sharding
- `references/headless-ui.md` - Dialog, Menu, Listbox testing
- `references/anti-patterns.md` - Full anti-pattern catalog

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nexus-labs-automation) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
