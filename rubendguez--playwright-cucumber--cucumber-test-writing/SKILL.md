---
name: cucumber-test-writing
description: Guide for writing Cucumber feature files and step definitions in this Playwright-Cucumber project. Use this when asked to create new tests, scenarios, or step definitions. Use when this capability is needed.
metadata:
  author: rubendguez
---

# Cucumber Test Writing Guide

When writing Cucumber tests for this Playwright-Cucumber project, follow these patterns and conventions:

## Project Structure
- Feature files: `tests/features/[FeatureName]/[FeatureName].feature`
- Step definitions: `tests/steps/[FeatureName].ts`
- Page objects: `tests/pages/[PageName].ts`

## Feature File Format
```gherkin
Feature: [Feature Name]
  As a [user type] I should be able to [action/goal]

  Scenario: [Descriptive scenario name]
    Given I am on the [page/application]
    When I [perform action]
    And I [additional action if needed]
    Then I should [expected outcome]
```

## Step Definition Patterns
```typescript
import { Given, Then, When } from '@cucumber/cucumber';
import { expect } from '@playwright/test';
import { Fixture } from 'tests/support/world';

Given('I am on the [page] website', async function (this: Fixture) {
  await this.[pageName].navigate();
});

When('I [action description]', async function (this: Fixture) {
  // Implementation using page object methods
});

Then('I should [expected outcome]', async function (this: Fixture) {
  // Assertions using expect from @playwright/test
});
```

## Page Object Pattern
```typescript
import { expect, Locator, Page } from '@playwright/test';

export default class [PageName]Page {
  constructor(private readonly page: Page) {}

  get [elementName](): Locator {
    return this.page.getByRole('[role]', { name: '[name]' });
  }

  async [methodName](): Promise<void> {
    // Implementation
  }

  async [assertionMethod](): Promise<boolean> {
    try {
      await expect(this.[element]).toBeVisible();
      return true;
    } catch {
      return false;
    }
  }
}
```

## Best Practices
1. Use descriptive scenario names that explain the business value
2. Keep scenarios focused on a single behavior
3. Use the existing Fixture type for step definitions
4. Leverage page object methods for reusability
5. Use environment variables for sensitive data (SF_USERNAME, SF_PASSWORD)
6. Follow the Given-When-Then structure strictly
7. Use Playwright locators with roles and accessible names
8. Include error handling for missing environment variables

## Running Tests
- All tests: `npm run test`
- Specific feature: `npx cucumber-js tests/features/[FeatureName]/[FeatureName].feature`
- Generate Allure reports: `npm run allure`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rubendguez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
