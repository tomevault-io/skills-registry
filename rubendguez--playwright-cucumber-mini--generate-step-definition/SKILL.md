---
name: generate-step-definitions
description: Generate Cucumber step definitions for Playwright test automation following project patterns Use when this capability is needed.
metadata:
  author: rubendguez
---

# Generate Step Definitions Skill

## Description
This skill generates Cucumber step definitions for Playwright-based test automation following the project's established patterns.

## When to Use
- When creating new step definitions for UI or API tests
- When adding new test scenarios that require additional step implementations
- When extending existing feature files with new steps

## Input Required
- **Step Type**: Given, When, or Then
- **Step Text**: The natural language description of the step (e.g., "I am on the login page")
- **Category**: UI or API
- **Implementation Details**: What the step should do

## Output
Generates TypeScript step definition files following the project's patterns.

## Pattern Rules

### File Structure
- **Location**: `tests/steps/{Category}/{FeatureName}.ts`
- **Import Structure**:
  ```typescript
  import { Given, When, Then } from '@cucumber/cucumber';
  import Fixture from '@fixtures/Fixture';
  import { expect } from '@fixtures/Playwright';
  ```

### Step Definition Patterns

#### Given Steps (Setup/Preconditions)
```typescript
Given('step text here', async function (this: Fixture) {
  // Setup code
  // Navigate to pages, set initial state, prepare data
});
```

**Example for UI:**
```typescript
Given('I am on the login page', async function (this: Fixture) {
  await this.login.navigate();
});
```

**Example for API:**
```typescript
Given('I send GET request to todos endpoint', async function (this: Fixture) {
  this.response = await this.request.get('https://jsonplaceholder.typicode.com/todos');
});
```

#### When Steps (Actions)
```typescript
When('step text here', async function (this: Fixture) {
  // Perform actions
  // Click buttons, fill forms, send requests
});
```

**Example for UI:**
```typescript
When('I enter valid credentials', async function (this: Fixture) {
  await this.login.login(this.utils.getUsername(), this.utils.getPassword());
  await expect(this.secureArea.secureAreaHeader).toBeVisible();
});
```

**Example for API:**
```typescript
When('I should receive a successful response with status code 200', async function (this: Fixture) {
  expect(this.response.status()).toBe(200);
});
```

#### Then Steps (Assertions/Verification)
```typescript
Then('step text here', async function (this: Fixture) {
  // Verify expected outcomes
  // Assert conditions, check responses
});
```

**Example for UI:**
```typescript
Then('I should be logged in', async function (this: Fixture) {
  await expect(this.page).toHaveURL(/\/secure/);
});
```

**Example for API:**
```typescript
Then('The response should contain a list of todos', async function (this: Fixture) {
  const responseBody = await this.response.json();
  expect(Array.isArray(responseBody)).toBe(true);
  expect(responseBody.length).toBeGreaterThan(0);
});
```

### Key Conventions

1. **Context Binding**: Always use `async function (this: Fixture)` to access the fixture context
2. **Await Async Operations**: All Playwright operations must be awaited
3. **Access Fixture Properties**:
   - `this.page` - Current page instance
   - `this.request` - API request context
   - `this.response` - API response object
   - `this.login`, `this.secureArea`, etc. - Page objects
   - `this.utils` - Utility functions
4. **Expectations**:
   - Use `expect` from `@fixtures/Playwright` for Playwright assertions
   - Use `await expect()` for async assertions (UI)
   - Use `expect()` without await for synchronous assertions (API responses)

### Parameter Handling

For steps with parameters, use Cucumber expressions:

**String Parameters:**
```typescript
Given('I enter {string} as username', async function (this: Fixture, username: string) {
  await this.login.enterUsername(username);
});
```

**Number Parameters:**
```typescript
Then('I should see {int} items', async function (this: Fixture, count: number) {
  const items = await this.page.locator('.item').count();
  expect(items).toBe(count);
});
```

**Table Parameters:**
```typescript
When('I fill the form with:', async function (this: Fixture, dataTable) {
  const data = dataTable.rowsHash();
  await this.page.fill('#username', data.username);
  await this.page.fill('#password', data.password);
});
```

### Best Practices

1. **Single Responsibility**: Each step should do one thing clearly
2. **Reusability**: Write steps that can be reused across scenarios
3. **Readability**: Step text should be natural language and self-explanatory
4. **Error Handling**: Let Playwright's built-in timeouts and retries handle errors
5. **Page Objects**: Interact with pages through page object methods, not direct selectors
6. **Utilities**: Use `this.utils` for common operations like getting credentials

### File Organization

- **UI Steps**: `tests/steps/UI/{Feature}.ts` - For browser interactions
- **API Steps**: `tests/steps/API/{Feature}.ts` - For API calls

### Example Complete File

```typescript
import { Given, When, Then } from '@cucumber/cucumber';
import Fixture from '@fixtures/Fixture';
import { expect } from '@fixtures/Playwright';

// Preconditions
Given('I am on the products page', async function (this: Fixture) {
  await this.page.goto('/products');
});

Given('I have {int} items in my cart', async function (this: Fixture, count: number) {
  // Implementation to add items to cart
  for (let i = 0; i < count; i++) {
    await this.page.click('.add-to-cart');
  }
});

// Actions
When('I click on the first product', async function (this: Fixture) {
  await this.page.click('.product-item:first-child');
});

When('I add the product to cart', async function (this: Fixture) {
  await this.page.click('#add-to-cart-button');
});

// Assertions
Then('I should see the product details', async function (this: Fixture) {
  await expect(this.page.locator('.product-details')).toBeVisible();
});

Then('The cart count should be {int}', async function (this: Fixture, expectedCount: number) {
  const cartCount = await this.page.locator('.cart-count').textContent();
  expect(parseInt(cartCount || '0')).toBe(expectedCount);
});
```

## Usage Examples

### Generate UI Step Definitions
"Create step definitions for a products feature with steps to navigate to products page, filter by category, and verify product count"

### Generate API Step Definitions  
"Create step definitions for a user API feature with steps to create a user, get user details, and verify the response"

### Generate Parameterized Steps
"Create a step definition that accepts a username and password as parameters and logs in the user"

## Notes
- Always ensure page objects exist before creating steps that reference them
- Consider adding the page object to Fixture.ts if it doesn't exist
- Keep step definitions focused and composable
- Use descriptive step text that matches feature file language

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rubendguez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
