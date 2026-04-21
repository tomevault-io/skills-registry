---
name: page-object-model
description: Comprehensive guide for Page Object Model governance and enforcement. Use this to validate, review, and enforce that ALL locators are properly contained in their corresponding Page Objects with strict adherence to POM principles. Use when this capability is needed.
metadata:
  author: rubendguez
---

# Page Object Model Enforcer

This skill serves as the **PRIMARY AUTHORITY** for Page Object Model governance in this Playwright-Cucumber project. Every piece of code that touches UI elements MUST be reviewed and validated against these strict POM principles.

## Core POM Rules

### 1. NO LOCATORS OUTSIDE PAGE OBJECTS
```typescript
// ❌ FORBIDDEN - Direct locators in step definitions
When('I click the login button', async function (this: Fixture) {
  await this.page.getByRole('button', { name: 'Log In' }).click(); // VIOLATION!
});

// ✅ CORRECT - Proper Page Object usage
When('I click the login button', async function (this: Fixture) {
  await this.loginPage.clickLoginButton();
});
```

### 2. REQUIRED PAGE OBJECT STRUCTURE
Every Page Object MUST follow this established pattern:
```typescript
import { expect, Locator, Page } from '@playwright/test';

export default class [PageName]Page {
  constructor(private readonly page: Page) {}

  // All locators as getters with explicit return types
  get [elementName](): Locator {
    return this.page.getByRole('[role]', { name: '[name]' });
  }

  // Navigation methods
  async navigate(): Promise<void> {
    await this.page.goto('[url]');
  }

  // Action methods - single responsibility
  async [actionName](): Promise<void> {
    await this.[elementGetter].click();
  }

  // Composite methods for workflows
  async [workflowName](): Promise<void> {
    // Multiple actions combined
  }

  // Assertion methods returning boolean
  async [assertionName](): Promise<boolean> {
    try {
      await expect(this.[elementGetter]).toBeVisible();
      return true;
    } catch {
      return false;
    }
  }
}
```

## Code Review Enforcement Rules

### 1. Step Definition Validation
**SCAN FOR VIOLATIONS:**
- `this.page.get*` - IMMEDIATE VIOLATION
- `this.page.locator*` - IMMEDIATE VIOLATION  
- `this.page.click*` - IMMEDIATE VIOLATION
- Any UI interaction not through Page Object - IMMEDIATE VIOLATION

**APPROVED PATTERNS:**
```typescript
// ✅ Correct - All UI interactions through Page Objects
Given('I am on the login page', async function (this: Fixture) {
  await this.loginPage.navigate();
});

When('I enter credentials {string} and {string}', async function (this: Fixture, username: string, password: string) {
  await this.loginPage.enterUsername(username);
  await this.loginPage.enterPassword(password);
});

Then('I should see the dashboard', async function (this: Fixture) {
  const isVisible = await this.dashboardPage.isVisible();
  expect(isVisible).toBe(true);
});
```

### 2. Page Object File Structure Validation
**MANDATORY FILE ORGANIZATION:**
```
tests/pages/
├── Login.ts           # LoginPage class
├── Dashboard.ts       # DashboardPage class  
├── Profile.ts         # ProfilePage class
└── [FeatureName].ts   # [FeatureName]Page class
```

### 3. Locator Strategy Enforcement
**APPROVED LOCATOR STRATEGIES (in order of preference):**
1. `getByRole()` with accessible names
2. `getByTestId()` for stable test attributes
3. `getByText()` for unique text content
4. `getByLabel()` for form elements
5. CSS selectors as LAST RESORT only

**FORBIDDEN LOCATOR STRATEGIES:**
- XPath selectors (except extreme cases)
- Brittle CSS selectors (nth-child, complex hierarchies)
- ID selectors without test-id prefix

### 4. Page Object Method Validation
```typescript
// ✅ CORRECT - Single responsibility methods
async clickLoginButton(): Promise<void> {
  await this.loginButton.click();
}

async enterUsername(username: string): Promise<void> {
  await this.usernameInput.fill(username);
}

// ✅ CORRECT - Composite workflow methods
async loginWithCredentials(username: string, password: string): Promise<void> {
  await this.enterUsername(username);
  await this.enterPassword(password);
  await this.clickLoginButton();
}

// ❌ VIOLATION - Business logic in Page Object
async loginWithValidation(username: string, password: string): Promise<void> {
  if (!username || !password) {
    throw new Error('Invalid credentials'); // BELONGS IN STEP DEFINITION!
  }
  // ... rest of method
}
```

## Validation Checklist

### For Every New/Modified File:

#### Step Definition Files (`tests/steps/*.ts`)
- [ ] Zero direct page interactions (`this.page.*`)
- [ ] All UI interactions through `this.[pageName].*`
- [ ] Business logic and validations present
- [ ] Proper error handling with context

#### Page Object Files (`tests/pages/*.ts`)
- [ ] Follows required structure pattern
- [ ] Constructor with `private readonly page: Page` only
- [ ] All locators as typed getters
- [ ] Single-responsibility action methods
- [ ] No business logic or test data
- [ ] Proper TypeScript return types

#### Support Files (`tests/support/*.ts`)
- [ ] Page Object instances properly initialized in Fixture
- [ ] No direct UI interactions in hooks
- [ ] Clean separation of concerns

## Enforcement Actions

### IMMEDIATE REJECTION CRITERIA:
1. Any locator usage outside Page Objects
2. Business logic inside Page Objects
3. Missing TypeScript types
4. Improper file naming/organization
5. Violation of single responsibility principle

### MANDATORY FIXES:
1. **Extract all locators** to appropriate Page Objects
2. **Create missing Page Objects** for new UI components
3. **Refactor step definitions** to use Page Object methods only
4. **Add proper typing** throughout the chain
5. **Update Fixture** to include new Page Object instances

## Code Examples for Common Scenarios

### Creating a New Page Object
```typescript
// tests/pages/NewFeature.ts
import { expect, Locator, Page } from '@playwright/test';

export default class NewFeaturePage {
  constructor(private readonly page: Page) {}

  get primaryButton(): Locator {
    return this.page.getByRole('button', { name: 'Primary Action' });
  }

  get statusMessage(): Locator {
    return this.page.getByTestId('status-message');
  }

  async navigate(): Promise<void> {
    await this.page.goto('/new-feature');
  }

  async clickPrimaryAction(): Promise<void> {
    await this.primaryButton.click();
  }

  async isStatusVisible(): Promise<boolean> {
    try {
      await expect(this.statusMessage).toBeVisible();
      return true;
    } catch {
      return false;
    }
  }
}
```

### Updating World/Fixture
```typescript
// tests/support/world.ts - ADD TO FIXTURE
export interface Fixture {
  page: Page;
  loginPage: LoginPage;
  newFeaturePage: NewFeaturePage; // ADD THIS
}

// In Before hook - ADD INITIALIZATION
this.newFeaturePage = new NewFeaturePage(this.page);
```

## Professional Standards

By following this Page Object Model skill, you commit to:
- **NEVER** write locators outside Page Objects
- **ALWAYS** maintain clean separation of concerns  
- **ENFORCE** the required POM structure consistently
- **REJECT** any code that violates these principles
- **GUIDE** others to follow proper POM practices

Remember: A properly implemented Page Object Model is not just code organization—it's the foundation of maintainable, scalable, and reliable test automation. Maintain these standards rigorously.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rubendguez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
