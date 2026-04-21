---
name: allure-reporting
description: Guide for working with Allure test reporting in this Cucumber project. Use this when asked about test reports, adding annotations, or improving test documentation. Use when this capability is needed.
metadata:
  author: rubendguez
---

# Allure Reporting Guide

This project uses Allure for test reporting. Here's how to effectively work with Allure features:

## Basic Allure Commands
```bash
# Generate and open reports
npm run allure:generate  # Generate report from test results
npm run allure:open      # Open report in browser
npm run allure           # Generate and open in one command
```

## Enhancing Test Reports

### 1. Adding Descriptions and Annotations
```typescript
import { Given, Then, When } from '@cucumber/cucumber';
import { allure } from 'allure-cucumberjs/reporter';

Given('I am on the Salesforce website', async function (this: Fixture) {
  await allure.epic('Authentication');
  await allure.feature('Login');
  await allure.story('User Login');
  await allure.severity('critical');
  
  await this.loginPage.navigate();
});
```

### 2. Adding Steps and Attachments
```typescript
When('I enter valid credentials', async function (this: Fixture) {
  await allure.step('Entering username', async () => {
    const username = process.env.SF_USERNAME || '';
    await this.loginPage.enterUsername(username);
  });

  await allure.step('Entering password', async () => {
    const password = process.env.SF_PASSWORD || '';
    await this.loginPage.enterPassword(password);
  });

  // Add screenshot for documentation
  const screenshot = await this.page.screenshot({ fullPage: true });
  await allure.attachment('Login Form Screenshot', screenshot, 'image/png');
});
```

### 3. Feature File Annotations
```gherkin
Feature: Login
  As a user I should be able to login into Salesforce
  
  @critical @smoke
  Scenario: Successful Login
    Given I am on the Salesforce website
    When I enter valid credentials
    Then I should be logged in successfully
    
  @negative @regression  
  Scenario: Failed Login with Invalid Credentials
    Given I am on the Salesforce website
    When I enter invalid credentials
    Then I should see an error message
```

### 4. Adding Test Links and Documentation
```typescript
Then('I should be logged in successfully', async function (this: Fixture) {
  await allure.link('Test Case', 'https://your-test-management-tool.com/test-123');
  await allure.link('Bug Report', 'https://your-issue-tracker.com/bug-456', 'issue');
  
  const isLoggedIn = await this.loginPage.isLoggedIn();
  
  if (!isLoggedIn) {
    // Add failure context
    const screenshot = await this.page.screenshot({ fullPage: true });
    await allure.attachment('Failure Screenshot', screenshot, 'image/png');
    
    const pageSource = await this.page.content();
    await allure.attachment('Page Source', pageSource, 'text/html');
  }
  
  expect(isLoggedIn).toBe(true);
});
```

## Report Organization

### 1. Cucumber Configuration for Allure
```javascript
// In cucumber.mjs
export default {
  // ... other config
  format: ['allure-cucumberjs/reporter'],
  formatOptions: {
    resultsDir: 'allure-results',
    labels: [
      {
        pattern: [/@feature:(.*)/],
        name: 'feature'
      },
      {
        pattern: [/@severity:(.*)/],
        name: 'severity'
      }
    ],
    links: [
      {
        pattern: [/@link:(.*)/],
        type: 'link'
      }
    ]
  }
};
```

### 2. Environment Information
```typescript
// In support/hooks.ts
import { BeforeAll, AfterAll } from '@cucumber/cucumber';
import { allure } from 'allure-cucumberjs/reporter';

BeforeAll(async function () {
  await allure.writeEnvironmentInfo({
    'Test Environment': process.env.TEST_ENV || 'development',
    'Browser': 'Chromium',
    'Base URL': process.env.SF_BASE_URL || 'default-url',
    'Playwright Version': require('@playwright/test/package.json').version,
    'Node Version': process.version,
    'OS': process.platform
  });
});
```

## Advanced Allure Features

### 1. Custom Categories for Failures
```javascript
// Create allure-results/categories.json
[
  {
    "name": "Authentication Failures",
    "matchedStatuses": ["failed"],
    "messageRegex": ".*login.*|.*authentication.*|.*credentials.*"
  },
  {
    "name": "Network Issues",
    "matchedStatuses": ["broken"],
    "messageRegex": ".*timeout.*|.*network.*|.*connection.*"
  },
  {
    "name": "Environment Issues",
    "matchedStatuses": ["failed"],
    "messageRegex": ".*SF_USERNAME.*|.*SF_PASSWORD.*|.*environment.*"
  }
]
```

### 2. Test Data Attachments
```typescript
// In step definitions
When('I use test data {string}', async function (this: Fixture, dataSet: string) {
  const testData = TestData.getDataSet(dataSet);
  
  // Attach test data for reference
  await allure.attachment(
    'Test Data Used', 
    JSON.stringify(testData, null, 2), 
    'application/json'
  );
  
  // Use the test data...
});
```

### 3. Performance Monitoring
```typescript
// Add timing information
Then('the page should load quickly', async function (this: Fixture) {
  const startTime = Date.now();
  
  await this.page.waitForLoadState('networkidle');
  
  const loadTime = Date.now() - startTime;
  
  await allure.parameter('Load Time (ms)', loadTime.toString());
  await allure.step(`Page loaded in ${loadTime}ms`, () => {});
  
  expect(loadTime).toBeLessThan(5000); // 5 second threshold
});
```

## Report Best Practices

1. **Consistent Tagging**: Use consistent tags across features for better filtering
2. **Meaningful Screenshots**: Take screenshots at key points, not just failures
3. **Detailed Steps**: Break down complex actions into smaller, reportable steps
4. **Environment Context**: Always include relevant environment information
5. **Failure Context**: Provide comprehensive failure information (screenshots, logs, page state)
6. **Performance Data**: Include timing information for critical operations
7. **Traceability**: Link tests to requirements or bug reports where applicable

## Viewing Reports
- Open the generated report in your browser
- Use filters to view specific test categories
- Review failure trends and patterns
- Export results for CI/CD integration
- Share reports with team members for collaboration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rubendguez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
