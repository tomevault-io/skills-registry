---
name: playwright
description: E2E testing with Playwright. Use when writing, debugging, or maintaining end-to-end tests. Covers locators (getByRole, getByLabel, getByTestId), assertions, Page Object Model, authentication setup, network mocking, debugging (trace viewer, UI mode), and test configuration. Triggers on E2E test files (.spec.ts), Playwright config, test debugging, flaky test fixes. Use when this capability is needed.
metadata:
  author: lincyaw
---

# Playwright E2E Testing

## Quick Start

```typescript
import { test, expect } from '@playwright/test'

test('user can login', async ({ page }) => {
  await page.goto('/login')
  await page.getByLabel('Username').fill('admin')
  await page.getByLabel('Password').fill('password')
  await page.getByRole('button', { name: 'Sign in' }).click()
  await expect(page).toHaveURL('/dashboard')
})
```

## Locator Priority (Best to Worst)

```typescript
// 1. BEST: Role-based (accessibility)
page.getByRole('button', { name: 'Submit' })
page.getByRole('textbox', { name: 'Email' })
page.getByRole('link', { name: 'Home' })

// 2. GOOD: Label/placeholder
page.getByLabel('Email address')
page.getByPlaceholder('Enter email')
page.getByText('Welcome')

// 3. ACCEPTABLE: Test IDs
page.getByTestId('submit-button')

// 4. AVOID: CSS selectors (brittle)
page.locator('.btn-primary')  // Breaks on style changes
```

## Locator Chaining

```typescript
// Filter by text
page.getByRole('listitem').filter({ hasText: 'Product 1' })

// Filter by child
page.getByRole('listitem').filter({
  has: page.getByRole('button', { name: 'Add' })
})

// Chain locators
page.getByRole('article')
    .filter({ hasText: 'Playwright' })
    .getByRole('button', { name: 'Read more' })

// Nth element
page.getByRole('listitem').first()
page.getByRole('listitem').nth(2)
page.getByRole('listitem').last()
```

## Assertions

```typescript
// Visibility
await expect(page.getByRole('button')).toBeVisible()
await expect(page.getByRole('dialog')).not.toBeVisible()

// Text
await expect(page.getByRole('heading')).toHaveText('Welcome')
await expect(page.getByRole('alert')).toContainText('error')

// State
await expect(page.getByRole('button')).toBeEnabled()
await expect(page.getByRole('checkbox')).toBeChecked()
await expect(page.getByRole('textbox')).toHaveValue('test@example.com')

// Count
await expect(page.getByRole('listitem')).toHaveCount(5)

// Page
await expect(page).toHaveURL('/dashboard')
await expect(page).toHaveTitle('Dashboard')
```

## Actions

```typescript
// Click
await page.getByRole('button').click()
await page.getByRole('button').dblclick()

// Input
await page.getByLabel('Email').fill('test@example.com')
await page.getByLabel('Email').clear()
await page.getByLabel('Search').press('Enter')

// Select
await page.getByLabel('Country').selectOption('usa')
await page.getByRole('checkbox').check()
await page.getByRole('radio', { name: 'Option A' }).check()

// File upload
await page.getByLabel('Upload').setInputFiles('file.pdf')
```

## Page Object Model

```typescript
// pages/LoginPage.ts
import { type Page, expect } from '@playwright/test'

export class LoginPage {
  constructor(private page: Page) {}

  readonly usernameInput = this.page.getByLabel('Username')
  readonly passwordInput = this.page.getByLabel('Password')
  readonly submitButton = this.page.getByRole('button', { name: 'Sign in' })

  async navigate() {
    await this.page.goto('/login')
  }

  async login(username: string, password: string) {
    await this.usernameInput.fill(username)
    await this.passwordInput.fill(password)
    await this.submitButton.click()
  }

  async assertSuccess() {
    await expect(this.page).not.toHaveURL(/login/)
  }
}

// Usage in test
test('login', async ({ page }) => {
  const loginPage = new LoginPage(page)
  await loginPage.navigate()
  await loginPage.login('admin', 'admin123')
  await loginPage.assertSuccess()
})
```

## Authentication Setup

```typescript
// auth.setup.ts
import { test as setup } from '@playwright/test'

setup('authenticate', async ({ page }) => {
  await page.goto('/login')
  await page.getByLabel('Username').fill('admin')
  await page.getByLabel('Password').fill('admin123')
  await page.getByRole('button', { name: 'Sign in' }).click()
  await page.waitForURL('/dashboard')
  await page.context().storageState({ path: 'tests/.auth/user.json' })
})

// playwright.config.ts
export default defineConfig({
  projects: [
    { name: 'setup', testMatch: /.*\.setup\.ts/ },
    {
      name: 'chromium',
      use: { storageState: 'tests/.auth/user.json' },
      dependencies: ['setup'],
    },
  ],
})

// Test without auth
test.use({ storageState: { cookies: [], origins: [] } })
```

## Waiting

```typescript
// Auto-waiting (default) - Playwright waits automatically
await page.getByRole('button').click()

// Explicit waits
await page.getByRole('button').waitFor({ state: 'visible' })
await page.waitForURL('/dashboard')
await page.waitForLoadState('networkidle')
await page.waitForResponse('/api/users')

// AVOID fixed timeouts
await page.waitForTimeout(2000)  // BAD
await expect(page.getByRole('table')).toBeVisible()  // GOOD
```

## Network Mocking

```typescript
// Mock API response
await page.route('/api/users', async route => {
  await route.fulfill({
    status: 200,
    contentType: 'application/json',
    body: JSON.stringify([{ id: 1, name: 'Mock User' }]),
  })
})

// Block requests
await page.route('**/*.{png,jpg}', route => route.abort())
```

## Debugging

```bash
# UI Mode (interactive)
npx playwright test --ui

# Debug mode (step through)
npx playwright test --debug

# View trace
npx playwright show-trace trace.zip

# Show report
npx playwright show-report
```

```typescript
// Pause in test
await page.pause()

// Console logs
page.on('console', msg => console.log('PAGE:', msg.text()))
```

## Configuration

```typescript
// playwright.config.ts
export default defineConfig({
  timeout: 60000,
  expect: { timeout: 10000 },
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 4 : undefined,
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'on-first-retry',
  },
})

// Per-test timeout
test.setTimeout(120000)
```

## Common Patterns

```typescript
// Table row interaction
const row = page.getByRole('row').filter({ hasText: 'John' })
await row.getByRole('button', { name: 'Edit' }).click()

// Modal handling
const modal = page.getByRole('dialog')
await expect(modal).toBeVisible()
await modal.getByRole('button', { name: 'Save' }).click()
await expect(modal).not.toBeVisible()

// Form validation
await page.getByRole('button', { name: 'Submit' }).click()
await expect(page.getByText('Email is required')).toBeVisible()
```

## Anti-Patterns

```typescript
// BAD: Arbitrary wait
await page.waitForTimeout(3000)
// GOOD: Wait for condition
await expect(page.getByRole('table')).toBeVisible()

// BAD: Brittle selector
await page.click('.btn-xyz-123')
// GOOD: Semantic selector
await page.getByRole('button', { name: 'Submit' }).click()

// BAD: Dependent tests
test('create', () => { /* creates user */ })
test('edit', () => { /* assumes user exists */ })
// GOOD: Independent tests with setup
test('edit', async () => {
  await createTestUser()
  // test edit
})
```

## Running Tests

```bash
npx playwright test                          # All tests
npx playwright test login.spec.ts            # Specific file
npx playwright test -g "should login"        # By title
npx playwright test --headed                 # See browser
npx playwright test --project=chromium       # Specific browser
```



# References

For more details, refer to the `./references/` directory for in-depth guides and best practices on using Playwright effectively in your E2E testing workflow.


```
.
├── accessibility-testing-java.md
├── accessibility-testing-js.md
├── actionability.md
├── api
│   ├── class-androiddevice.md
│   ├── class-androidinput.md
│   ├── class-android.md
│   ├── class-androidsocket.md
│   ├── class-androidwebview.md
│   ├── class-apirequestcontext.md
│   ├── class-apirequest.md
│   ├── class-apiresponseassertions.md
│   ├── class-apiresponse.md
│   ├── class-browsercontext.md
│   ├── class-browser.md
│   ├── class-browserserver.md
│   ├── class-browsertype.md
│   ├── class-cdpsessionevent.md
│   ├── class-cdpsession.md
│   ├── class-clock.md
│   ├── class-consolemessage.md
│   ├── class-coverage.md
│   ├── class-dialog.md
│   ├── class-download.md
│   ├── class-electronapplication.md
│   ├── class-electron.md
│   ├── class-elementhandle.md
│   ├── class-error.md
│   ├── class-filechooser.md
│   ├── class-formdata.md
│   ├── class-framelocator.md
│   ├── class-frame.md
│   ├── class-genericassertions.md
│   ├── class-jshandle.md
│   ├── class-keyboard.md
│   ├── class-locatorassertions.md
│   ├── class-locator.md
│   ├── class-logger.md
│   ├── class-mouse.md
│   ├── class-pageagent.md
│   ├── class-pageassertions.md
│   ├── class-page.md
│   ├── class-playwrightassertions.md
│   ├── class-playwrightexception.md
│   ├── class-playwright.md
│   ├── class-request.md
│   ├── class-requestoptions.md
│   ├── class-response.md
│   ├── class-route.md
│   ├── class-selectors.md
│   ├── class-snapshotassertions.md
│   ├── class-timeouterror.md
│   ├── class-touchscreen.md
│   ├── class-tracing.md
│   ├── class-video.md
│   ├── class-weberror.md
│   ├── class-websocketframe.md
│   ├── class-websocket.md
│   ├── class-websocketroute.md
│   ├── class-worker.md
│   └── params.md
├── api-testing-csharp.md
├── api-testing-java.md
├── api-testing-js.md
├── api-testing-python.md
├── aria-snapshots.md
├── auth.md
├── best-practices-js.md
├── browser-contexts.md
├── browsers.md
├── canary-releases-js.md
├── chrome-extensions-js-python.md
├── ci-intro.md
├── ci.md
├── clock.md
├── codegen-intro.md
├── codegen.md
├── debug.md
├── dialogs.md
├── docker.md
├── downloads.md
├── emulation.md
├── evaluating.md
├── events.md
├── extensibility.md
├── frames.md
├── getting-started-vscode-js.md
├── handles.md
├── images
│   ├── cft-logo-change.png
│   ├── getting-started
│   │   ├── codegen-csharp.png
│   │   ├── codegen-java.png
│   │   ├── codgen-js.png
│   │   ├── codgen-python.png
│   │   ├── debug-mode.png
│   │   ├── error-messaging.png
│   │   ├── fix-with-ai.png
│   │   ├── global-setup.png
│   │   ├── html-report-basic.png
│   │   ├── html-report-detail.png
│   │   ├── html-report-failed-tests.png
│   │   ├── html-report-open.png
│   │   ├── html-report.png
│   │   ├── html-report-trace.png
│   │   ├── install-browsers.png
│   │   ├── install-playwright.png
│   │   ├── live-debugging.png
│   │   ├── pick-locator-csharp.png
│   │   ├── pick-locator-java.png
│   │   ├── pick-locator-js.png
│   │   ├── pick-locator.png
│   │   ├── pick-locator-python.png
│   │   ├── record-at-cursor.png
│   │   ├── record-new-test.png
│   │   ├── record-test-csharp.png
│   │   ├── record-test-java.png
│   │   ├── record-test-js.png
│   │   ├── record-test-python.png
│   │   ├── run-all-tests.png
│   │   ├── run-single-test.png
│   │   ├── run-tests-cli.png
│   │   ├── run-tests-debug.png
│   │   ├── run-tests-pick-locator.png
│   │   ├── selecting-configuration.png
│   │   ├── select-projects.png
│   │   ├── setup-tests.png
│   │   ├── show-browser.png
│   │   ├── testing-sidebar.png
│   │   ├── trace-viewer-debug.png
│   │   ├── trace-viewer-failed-test.png
│   │   ├── trace-viewer.png
│   │   ├── ui-mode-error.png
│   │   ├── ui-mode-pick-locator.png
│   │   ├── ui-mode.png
│   │   └── vscode-extension.png
│   ├── speedboard.png
│   ├── test-agents
│   │   ├── generator-prompt.png
│   │   ├── healer-prompt.png
│   │   └── planner-prompt.png
│   ├── timeline.png
│   └── vscode-projects-section.png
├── input.md
├── intro-csharp.md
├── intro-java.md
├── intro-js.md
├── intro-python.md
├── junit-java.md
├── languages.md
├── library-csharp.md
├── library-js.md
├── library-python.md
├── locators.md
├── mock-browser-js.md
├── mock.md
├── navigations.md
├── network.md
├── other-locators.md
├── pages.md
├── pom.md
├── protractor-js.md
├── puppeteer-js.md
├── release-notes-csharp.md
├── release-notes-java.md
├── release-notes-js.md
├── release-notes-python.md
├── running-tests-csharp.md
├── running-tests-java.md
├── running-tests-js.md
├── running-tests-python.md
├── screenshots.md
├── selenium-grid.md
├── service-workers-js-python.md
├── test-agents-js.md
├── test-annotations-js.md
├── test-api
│   ├── class-fixtures.md
│   ├── class-fullconfig.md
│   ├── class-fullproject.md
│   ├── class-location.md
│   ├── class-testconfig.md
│   ├── class-testinfoerror.md
│   ├── class-testinfo.md
│   ├── class-test.md
│   ├── class-testoptions.md
│   ├── class-testproject.md
│   ├── class-teststepinfo.md
│   └── class-workerinfo.md
├── test-assertions-csharp-java-python.md
├── test-assertions-js.md
├── test-cli-js.md
├── test-components-js.md
├── test-configuration-js.md
├── test-fixtures-js.md
├── test-global-setup-teardown-js.md
├── testing-library-js.md
├── test-parallel-js.md
├── test-parameterize-js.md
├── test-projects-js.md
├── test-reporter-api
│   ├── class-reporter.md
│   ├── class-suite.md
│   ├── class-testcase.md
│   ├── class-testerror.md
│   ├── class-testresult.md
│   └── class-teststep.md
├── test-reporters-js.md
├── test-retries-js.md
├── test-runners-csharp.md
├── test-runners-java.md
├── test-runners-python.md
├── test-sharding-js.md
├── test-snapshots-js.md
├── test-timeouts-js.md
├── test-typescript-js.md
├── test-ui-mode-js.md
├── test-use-options-js.md
├── test-webserver-js.md
├── threading-java.md
├── touch-events.md
├── trace-viewer-intro-csharp.md
├── trace-viewer-intro-java-python.md
├── trace-viewer-intro-js.md
├── trace-viewer.md
├── videos.md
├── webview2.md
├── writing-tests-csharp.md
├── writing-tests-java.md
├── writing-tests-js.md
└── writing-tests-python.md
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lincyaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
