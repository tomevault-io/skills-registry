---
name: playwright-tests
description: Writes and maintains reliable Playwright end-to-end tests using accessible selectors (getByRole, getByLabel) and web-first assertions. Use when creating e2e tests, fixing flaky tests, or reviewing test code for best practices. Use when this capability is needed.
metadata:
  author: hasparus
---

# Writing Playwright Tests

Tests assert observable behavior from a user's perspective. If a test fails, something is genuinely broken—not timing, not selectors, not environment.

## Selector Priority

Use accessible selectors in this order. These query the accessibility tree, making tests resilient to DOM changes.

| Priority | Method | Example |
|----------|--------|---------|
| 1 | `getByRole()` | `getByRole('button', { name: 'Submit' })` |
| 2 | `getByLabel()` | `getByLabel('Email address')` |
| 3 | `getByPlaceholder()` | `getByPlaceholder('Search...')` |
| 4 | `getByText()` | `getByText('Welcome', { exact: true })` |
| 5 | `getByAltText()` | `getByAltText('Company logo')` |
| 6 | Composed locators | `page.locator('section').filter({ has: getByRole('heading', { name: 'Featured' }) })` |
| 7 | `aria-label` attribute | `locator('[aria-label="Link title"]')` |

**Never use**: CSS classes, nth-child, generated IDs, XPath.

## Core Patterns

### Waiting

```ts
// WRONG - arbitrary timeout
await page.click('#submit');
await page.waitForTimeout(2000);

// CORRECT - web-first assertion auto-retries
await page.getByRole('button', { name: 'Submit' }).click();
await expect(page.getByText('Success')).toBeVisible();
```

### List Counts

```ts
// WRONG - .all() doesn't wait
const items = await page.getByRole('listitem').all();
expect(items.length).toBe(5);

// CORRECT
await expect(page.getByRole('listitem')).toHaveCount(5);
```

### Hydration

```ts
// Wait for interactivity signal before filling uncontrolled inputs
await expect(page.getByRole('button', { name: 'Add' })).toBeEnabled();
await page.getByPlaceholder('Number').fill('12345');
```

### Optimistic UI (temp ID → real ID)

```ts
await page.getByPlaceholder('Number').fill('12345');
await page.getByRole('button', { name: 'Add' }).click();

// Confirm action started
await expect(page.getByPlaceholder('Number')).toHaveValue('');

// Wait for real ID
const newItem = page.locator('a[href^="/item/"]').filter({ hasText: '#12345' });
await expect(async () => {
  const href = await newItem.getAttribute('href');
  expect(href).not.toContain('temp-');
}).toPass({ timeout: 15_000 });
```

### Dialogs

```ts
// Set up handler BEFORE triggering
page.once('dialog', dialog => dialog.accept());
await page.getByTitle('Delete').click();
```

## Test Data Isolation

```ts
const timestamp = Date.now();
const testName = `Test Item ${timestamp}`;

// Dedicated ID ranges per test file
const getTestId = () => 60_000 + Math.floor(Math.random() * 10_000);
```

## CRUD Verification Workflow

Copy and track:

```
- [ ] Perform action
- [ ] Wait for save completion
- [ ] Reload page
- [ ] Verify persisted state
```

```ts
await page.getByLabel('Title').fill('New Title');
await page.getByRole('button', { name: 'Save' }).click();
await expect(page.getByText(/unsaved/)).not.toBeVisible({ timeout: 15_000 });

await page.reload();
await page.waitForLoadState('networkidle');
await expect(page.getByLabel('Title')).toHaveValue('New Title');
```

## Timeout Reference

| Operation | Timeout |
|-----------|---------|
| Simple visibility | 5s (default) |
| Mutation completion | 10-15s |
| Page load after reload | 15s |

Custom polling:

```ts
await expect(async () => {
  const count = await page.getByRole('listitem').count();
  expect(count).toBeGreaterThanOrEqual(5);
}).toPass({ timeout: 10_000, intervals: [100, 250, 500, 1000] });
```

## Configuration

```ts
export default defineConfig({
  retries: process.env.CI ? 2 : 1,
  fullyParallel: true,
  workers: process.env.CI ? 1 : 2,
  use: {
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    navigationTimeout: 30_000,
  },
});
```

## Debugging Flaky Tests

1. Run with trace: `bunx playwright test --trace on`
2. View trace: `bunx playwright show-trace trace.zip`
3. Check for: race conditions, stale selectors, network timing, shared state

## Unfixable Tests

```ts
test.skip('drag link to topic', async ({ page }) => {
  // TODO: dnd-kit keyboard simulation incompatible with Playwright
  // Manual testing required. Issue: #123
});
```

## Pre-Commit Checklist

```
- [ ] No waitForTimeout() calls
- [ ] Selectors use getByRole/getByLabel/getByPlaceholder
- [ ] Test data includes timestamp
- [ ] CRUD operations verify persistence after reload
- [ ] Timeouts explicit where needed
- [ ] No shared state between tests
- [ ] Dialog handlers set up before triggering action
```

## Reference

For role options (pressed, checked, expanded, level, etc.) and locator composition (.filter(), scoped .locator()), see:
- [Playwright Locators](https://playwright.dev/docs/locators)
- [Testing Library Guiding Principles](https://testing-library.com/docs/guiding-principles)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hasparus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
