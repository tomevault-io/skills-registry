---
name: scaffolding-playwright-projects
description: Generates a complete, production-ready Playwright automation framework in TypeScript. Includes Page Object Model (POM) architecture, custom reporters, and adherence to creating robust automation guidelines. Use when the user wants to scaffold a new testing project, sets up a framework "from scratch", or requests a specific domain automation setup.
metadata:
  author: vikaskpawar
---

# Scaffolding Playwright Projects

## When to use this skill
- Starting a new end-to-end (E2E) testing project from scratch.
- Modernizing a legacy Selenium or Cypress suite to Playwright.
- Implementing Page Object Model (POM) architecture for better maintainability.
- Requiring a production-grade setup with CI/CD integration, custom reporters, and environment-specific configs.

## Workflow
1. **Requirements Gathering**: 
   - [ ] Confirm base URL and project name.
   - [ ] Identify key browser targets (Chromium, Firefox, Webkit).
   - [ ] Define environment strategy (Dev, Stage, Prod).
2. **Framework Scaffolding**: 
   - [ ] Initialize `package.json` with Playwright and TypeScript.
   - [ ] Create folder structure: `pages/`, `tests/`, `utils/`, `data/`.
   - [ ] Generate `playwright.config.ts` with robust defaults (retries, timeouts, reporters).
3. **POM Implementation**: 
   - [ ] Scaffold base page classes and specific page objects.
   - [ ] Implement utility methods for common actions (authentication, database seeding).
4. **Validation**: 
   - [ ] Run a sample "sanity" test to verify configuration.
   - [ ] Check linting and TypeScript compilation.

## Instructions

### 1. Framework Structure
Always enforce this directory structure for scalability:
```text
/
├── tests/              # Test files (.spec.ts)
├── pages/              # Page Object Models
├── utils/              # Helper functions and hooks
├── fixtures/           # Test fixtures (data or custom objects)
├── reports/            # Output directory for test results
├── playwright.config.ts # Core configuration
└── package.json
```

### 2. Standard `playwright.config.ts` Template
Generate a config that includes:
- **Parallelism**: Fully parallel by default.
- **Reporting**: List, HTML, and JUnit (for CI).
- **Traces**: `on-first-retry` to save storage while assisting debugging.
```typescript
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './tests',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: [['html'], ['list']],
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
  },
  projects: [
    { name: 'chromium', use: { ...devices['Desktop Chrome'] } },
    { name: 'firefox', use: { ...devices['Desktop Firefox'] } },
  ],
});
```

### 3. Page Object Model (POM) Guidelines
- **Encapsulation**: Components should hide internal selectors and expose only high-level actions.
- **Locators**: Prefer User-Facing Locators (e.g., `getByRole`, `getByText`) over fragile CSS/XPath.
- **Assertions**: Keep assertions in the test files, not inside Page Objects (unless for state verification).

## Resources
- [Example Page Object](examples/base-page.ts)
- [Custom Reporter Template](resources/custom-reporter.ts)

## Implementation Details

### Example Page Object
Create in `.agent/skills/scaffolding-playwright-projects/examples/base-page.ts`:
```typescript
import { Page, Locator } from '@playwright/test';

export class BasePage {
  readonly page: Page;

  constructor(page: Page) {
    this.page = page;
  }

  async navigate(path: string) {
    await this.page.goto(path);
  }

  async waitForNetworkIdle() {
    await this.page.waitForLoadState('networkidle');
  }
}
```

### Custom Reporter
Create in `.agent/skills/scaffolding-playwright-projects/resources/custom-reporter.ts`:
```typescript
import { Reporter, TestCase, TestResult } from '@playwright/test/reporter';

class MyReporter implements Reporter {
  onTestEnd(test: TestCase, result: TestResult) {
    if (result.status !== 'passed') {
      console.log(`❌ Test ${test.title} failed with status: ${result.status}`);
    }
  }
}
export default MyReporter;
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vikaskpawar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
