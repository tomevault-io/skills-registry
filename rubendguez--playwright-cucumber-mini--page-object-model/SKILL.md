---
name: page-object-model
description: Standards for creating Page Object Model classes in Playwright-Cucumber. Use when creating or modifying page objects in tests/pages/ directory. Use when this capability is needed.
metadata:
  author: rubendguez
---

# SKILL: Page Object Model Pattern

This document defines the standards and best practices for creating Page Object Model (POM) classes in this Playwright-Cucumber TypeScript project.

## Purpose

Ensure consistency, maintainability, and readability across all page object implementations.

## Core Principles

### 1. Constructor Pattern
- **MUST** use an empty constructor with only a single parameter
- **MUST** use `private readonly page: Page` as the only constructor parameter
- **NO** initialization logic in the constructor
- **NO** additional parameters

```typescript
constructor(private readonly page: Page) {}
```

### 2. Import Standards
- **MUST** import `Page` and `Locator` from `@fixtures/Playwright`
- Order imports logically (Playwright types first, then other dependencies)

```typescript
import { Page, Locator } from '@fixtures/Playwright';
```

### 3. Locator Pattern
- **ALL** locators **MUST** be defined as public getters
- **MUST** explicitly declare return type as `Locator`
- **MUST** use Playwright's recommended selector strategies in this order of preference:
  1. `getByRole()` - Most robust, accessibility-first
  2. `getByLabel()` - For form fields
  3. `getByPlaceholder()` - When appropriate
  4. `getByText()` - For text content
  5. `getByTestId()` - Last resort for elements without semantic meaning
- **AVOID** CSS selectors and XPath unless absolutely necessary

```typescript
get usernameInput(): Locator {
  return this.page.getByRole('textbox', { name: 'Username' });
}

get submitButton(): Locator {
  return this.page.getByRole('button', { name: ' Login' });
}

get secureAreaHeader(): Locator {
  return this.page.getByRole('heading', { name: 'Secure Area', exact: true });
}
```

### 4. Method Standards
- **MUST** declare explicit return types for all methods
- Use `Promise<void>` for async methods that don't return values
- Use `void` for synchronous methods that don't return values
- Use specific return types (`Promise<string>`, `Promise<boolean>`, etc.) when returning values
- Methods should be `async` when performing actions or interactions
- Keep methods focused on single responsibilities

```typescript
async navigate(): Promise<void> {
  await this.page.goto('https://example.com');
}

async login(username: string, password: string): Promise<void> {
  await this.usernameInput.fill(username);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
}

async getHeaderText(): Promise<string> {
  return await this.secureAreaHeader.textContent() ?? '';
}

async isVisible(): Promise<boolean> {
  return await this.secureAreaHeader.isVisible();
}
```

### 5. Class Structure Order
Organize class members in the following order:
1. Constructor
2. Locator getters (grouped logically by feature/section)
3. Navigation methods
4. Action methods
5. Assertion/verification helper methods

### 6. Naming Conventions
- **Class names**: Use PascalCase with "Page" suffix (e.g., `LoginPage`, `SecureAreaPage`)
- **Locator getters**: Use camelCase describing the element (e.g., `usernameInput`, `submitButton`, `logoutButton`)
- **Methods**: Use camelCase describing the action (e.g., `navigate`, `login`, `clickSubmit`)
- Be descriptive but concise

### 7. File Organization
- One page object class per file
- File name matches class name (e.g., `Login.ts` for `LoginPage`)
- Store in `tests/pages/` directory
- Use default exports

```typescript
export default class LoginPage {
  // implementation
}
```

## Complete Example Template

```typescript
import { Page, Locator } from '@fixtures/Playwright';

export default class ExamplePage {
  constructor(private readonly page: Page) {}

  // Locator Getters
  get primaryInput(): Locator {
    return this.page.getByRole('textbox', { name: 'Primary Input' });
  }

  get submitButton(): Locator {
    return this.page.getByRole('button', { name: 'Submit' });
  }

  get confirmationMessage(): Locator {
    return this.page.getByText('Success!');
  }

  // Navigation Methods
  async navigate(): Promise<void> {
    await this.page.goto('https://example.com');
  }

  // Action Methods
  async fillAndSubmit(value: string): Promise<void> {
    await this.primaryInput.fill(value);
    await this.submitButton.click();
  }

  async clickSubmit(): Promise<void> {
    await this.submitButton.click();
  }

  // Verification Methods
  async isConfirmationVisible(): Promise<boolean> {
    return await this.confirmationMessage.isVisible();
  }

  async getConfirmationText(): Promise<string> {
    return await this.confirmationMessage.textContent() ?? '';
  }
}
```

## Best Practices

### DO
✅ Use semantic selectors (roles, labels, text)
✅ Make locators reusable through getters
✅ Keep methods small and focused
✅ Use explicit return types
✅ Use `async/await` for asynchronous operations
✅ Return promises from async methods
✅ Group related locators together
✅ Use meaningful, descriptive names

### DON'T
❌ Initialize values in the constructor
❌ Add multiple parameters to the constructor
❌ Use CSS selectors or XPath as first choice
❌ Omit return types from methods
❌ Create methods that do too many things
❌ Expose the page object directly
❌ Use magic strings (extract to constants if needed)
❌ Make locators private

## Accessibility-First Approach

Always prefer selectors that reflect how users interact with the page:
- Use `getByRole()` with appropriate ARIA roles
- Include `name` option for clarity and specificity
- Use `exact: true` when you need precise matching
- This approach makes tests more resilient to implementation changes

## Type Safety

- Leverage TypeScript's type system
- All parameters should have explicit types
- All return values should have explicit types
- Import types from Playwright fixtures, not directly from `@playwright/test`

## Error Prevention

- Use `readonly` for the page parameter to prevent accidental reassignment
- Use getters for locators to ensure fresh locators on each access
- Avoid storing element handles; always use locators
- Let Playwright handle waiting and retrying through locators

---

**Remember**: Consistency across page objects makes the entire test suite more maintainable and easier for team members to understand and contribute to.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rubendguez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
