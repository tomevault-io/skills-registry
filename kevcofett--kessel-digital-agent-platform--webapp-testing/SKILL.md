---
name: webapp-testing
description: > Use when this capability is needed.
metadata:
  author: kevcofett
---

# Webapp Testing

## Prerequisites

Requires Playwright MCP server configured in mcp.json, or Playwright installed locally.

## Commands

- `/webtest <description>` — Generate and run a test from a plain English description
- `/webtest record <url>` — Open a browser and record user actions as a test
- `/webtest run <file>` — Run an existing test file
- `/webtest report` — Show results from the last test run

## Procedure

### Phase 1: Understand the Flow

Parse the user's plain English description into test steps:

Input: "Log in with test credentials, navigate to settings, change the display name, verify it saved"

Output steps:
1. Navigate to login page
2. Fill email and password fields
3. Click login button
4. Wait for dashboard to load
5. Click settings link
6. Clear display name field
7. Type new display name
8. Click save button
9. Verify success message appears
10. Verify display name updated

### Phase 2: Generate Test

Write a Playwright test spec:

```typescript
import { test, expect } from '@playwright/test';

test('update display name in settings', async ({ page }) => {
  await page.goto('/login');
  await page.fill('[name="email"]', process.env.TEST_EMAIL);
  await page.fill('[name="password"]', process.env.TEST_PASSWORD);
  await page.click('button[type="submit"]');
  await page.waitForURL('/dashboard');
  // ... remaining steps
});
```

### Phase 3: Run

Execute the test using either:
- Playwright MCP server (browser_navigate, browser_click, browser_fill_form, etc.)
- Local Playwright CLI: `npx playwright test`

### Phase 4: Report

For each test:
- Status: PASS / FAIL
- Duration
- Screenshots on failure
- Error details with line reference
- Suggested fix if failed

## Test Patterns

- Login flows
- Form submissions
- Navigation and routing
- CRUD operations
- Error state handling
- Responsive layout verification

## MCMAP Usage

Primary test targets:
- Power Apps portal pages
- Copilot Studio web chat widget
- SharePoint document libraries
- Dataverse model-driven apps (via browser automation)

## Safety Rules

- Never use production credentials in tests — use test/sandbox accounts only
- Never modify production data — always target sandbox environments
- Store test credentials in environment variables, never in test files

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevcofett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
