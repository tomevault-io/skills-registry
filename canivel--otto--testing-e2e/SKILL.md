---
name: testing-e2e
description: Use when writing or fixing end-to-end tests with Playwright. Covers page navigation, selectors, assertions, fixtures, authentication flows, and visual regression testing.
metadata:
  author: canivel
---

# E2E Testing with Playwright

## Core Principles

- Test critical user journeys, not every edge case. Reserve unit/integration tests for details.
- Use resilient locators: `getByRole`, `getByLabel`, `getByText`. Avoid CSS selectors.
- Each test must be independent. Never rely on execution order or shared state.

## Basic Page Navigation and Assertions

```ts
import { test, expect } from '@playwright/test';

test('homepage loads and shows heading', async ({ page }) => {
  await page.goto('/');
  await expect(page).toHaveTitle(/Otto/);
  await expect(page.getByRole('heading', { name: /welcome/i })).toBeVisible();
});

test('navigates to dashboard on login', async ({ page }) => {
  await page.goto('/login');
  await page.getByLabel('Email').fill('user@example.com');
  await page.getByLabel('Password').fill('password123');
  await page.getByRole('button', { name: /sign in/i }).click();

  await expect(page).toHaveURL(/\/dashboard/);
  await expect(page.getByText('Welcome back')).toBeVisible();
});
```

## Selectors - Preferred Order

```ts
// 1. Role-based (best)
page.getByRole('button', { name: /submit/i });
page.getByRole('link', { name: /settings/i });
page.getByRole('textbox', { name: /search/i });

// 2. Label-based (forms)
page.getByLabel('Email address');
page.getByPlaceholder('Search...');

// 3. Text-based
page.getByText('No results found');

// 4. Test ID (last resort for elements without accessible names)
page.getByTestId('complex-chart-widget');
```

## Authentication Fixture

```ts
// fixtures/auth.ts
import { test as base, expect } from '@playwright/test';

type AuthFixtures = {
  authenticatedPage: Page;
};

export const test = base.extend<AuthFixtures>({
  authenticatedPage: async ({ page }, use) => {
    await page.goto('/login');
    await page.getByLabel('Email').fill(process.env.TEST_USER_EMAIL!);
    await page.getByLabel('Password').fill(process.env.TEST_USER_PASSWORD!);
    await page.getByRole('button', { name: /sign in/i }).click();
    await expect(page).toHaveURL(/\/dashboard/);
    await use(page);
  },
});

// Using storageState for faster auth (preferred for multiple tests)
// playwright.config.ts
export default defineConfig({
  projects: [
    { name: 'setup', testMatch: /.*\.setup\.ts/ },
    {
      name: 'chromium',
      use: { storageState: '.auth/user.json' },
      dependencies: ['setup'],
    },
  ],
});

// auth.setup.ts
import { test as setup, expect } from '@playwright/test';

setup('authenticate', async ({ page }) => {
  await page.goto('/login');
  await page.getByLabel('Email').fill('user@example.com');
  await page.getByLabel('Password').fill('password123');
  await page.getByRole('button', { name: /sign in/i }).click();
  await expect(page).toHaveURL(/\/dashboard/);
  await page.context().storageState({ path: '.auth/user.json' });
});
```

## Waiting and Async Patterns

```ts
// Playwright auto-waits for elements. Explicit waits only when needed.
test('loads data after API call', async ({ page }) => {
  await page.goto('/projects');

  // Wait for network response
  const responsePromise = page.waitForResponse('**/api/projects');
  await page.getByRole('button', { name: /refresh/i }).click();
  await responsePromise;

  await expect(page.getByRole('listitem')).toHaveCount(5);
});

// Wait for navigation
await Promise.all([
  page.waitForURL('**/dashboard'),
  page.getByRole('link', { name: /dashboard/i }).click(),
]);
```

## Visual Regression

```ts
test('dashboard matches visual snapshot', async ({ page }) => {
  await page.goto('/dashboard');
  await expect(page.getByText('Welcome')).toBeVisible(); // wait for content
  await expect(page).toHaveScreenshot('dashboard.png', { maxDiffPixelRatio: 0.01 });
});

test('button states', async ({ page }) => {
  await page.goto('/components');
  const button = page.getByRole('button', { name: /primary/i });
  await expect(button).toHaveScreenshot('button-default.png');

  await button.hover();
  await expect(button).toHaveScreenshot('button-hover.png');
});
```

## Form and Interaction Patterns

```ts
test('creates a new project', async ({ page }) => {
  await page.goto('/projects/new');

  await page.getByLabel('Project name').fill('My Project');
  await page.getByLabel('Description').fill('A test project');
  await page.getByRole('combobox', { name: /template/i }).selectOption('blank');
  await page.getByRole('checkbox', { name: /public/i }).check();
  await page.getByRole('button', { name: /create project/i }).click();

  await expect(page).toHaveURL(/\/projects\/\d+/);
  await expect(page.getByText('My Project')).toBeVisible();
});

// File upload
test('uploads avatar', async ({ page }) => {
  await page.goto('/settings/profile');
  const fileInput = page.getByLabel('Upload avatar');
  await fileInput.setInputFiles('test-data/avatar.png');
  await expect(page.getByRole('img', { name: /avatar/i })).toBeVisible();
});
```

## Playwright Config Essentials

```ts
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  testDir: './e2e',
  timeout: 30_000,
  retries: process.env.CI ? 2 : 0,
  use: {
    baseURL: 'http://localhost:3000',
    screenshot: 'only-on-failure',
    trace: 'on-first-retry',
  },
  webServer: {
    command: 'npm run dev',
    port: 3000,
    reuseExistingServer: !process.env.CI,
  },
});
```

## Anti-Patterns

- NEVER use `page.waitForTimeout` with hardcoded delays. Use proper locator assertions.
- NEVER chain tests that depend on each other. Each test must stand alone.
- NEVER use CSS class selectors (`page.locator('.btn-primary')`). Use roles and text.
- NEVER test third-party UI (OAuth providers, payment forms). Mock those boundaries.
- NEVER skip flaky tests permanently. Fix the root cause or add proper waits.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canivel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
