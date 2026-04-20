---
name: playwright-pom-scaffolder
description: This skill creates a complete, production-ready Playwright TypeScript/JavaScript automation framework from scratch using the Page Object Model (POM) pattern. It includes custom reporters, best practices, and automated scaffolding based on user-provided domains and instructions. Use when this capability is needed.
metadata:
  author: marufcode
---

# Playwright POM Scaffolder

## Purpose

To automate the creation of a professional-grade Playwright automation framework. This skill transforms a simple domain name and a set of automation goals into a fully structured project following industry best practices: Page Object Model (POM), strict TypeScript configuration, custom reporters, and modular design.

## When to Use

Use this skill when:
- You need to start a new web automation project from scratch.
- You want to ensure the project follows the Page Object Model (POM) pattern.
- You need a custom reporter setup (e.g., HTML + JSON + Console).
- You want to standardize testing infrastructure across teams.

## Core Capabilities

1. **Framework Scaffolding**: Automatically creates directories for `pages`, `tests`, `utils`, and `reporters`.
2. **POM Implementation**: Generates BasePage classes and specific Page Objects based on the target site.
3. **Configuration Generation**: Creates a robust `playwright.config.ts` with multi-browser support, tracing, and custom reporting.
4. **Environment Setup**: Initializes `package.json` and installs necessary dependencies (`@playwright/test`, `typescript`, etc.).
5. **Instructional Integration**: Converts natural language automation requirements into actual test cases.

## Workflow

### Phase 1: Requirement Gathering

The assistant must first ask the user for:
1. **Target Domain/URL**: e.g., `https://example.com`
2. **Automation Goals**: e.g., "Login flow, checkout process, and contact form validation."
3. **Preferred Language**: TypeScript (recommended) or JavaScript.

### Phase 2: Project Initialization

Execute these steps to set up the foundation:

```bash
# 1. Create project directory
mkdir playwright-automation
cd playwright-automation

# 2. Initialize npm
npm init -y

# 3. Install Playwright
npm install -D @playwright/test typescript @types/node
npx playwright install chromium
```

### Phase 3: Directory Structure

Create a clean, modular structure:

```bash
mkdir -p pages tests utils reporters
```

### Phase 4: Scaffolding Configuration

The assistant generates a `playwright.config.ts` that includes:
- Parallel execution settings.
- Retries and timeouts.
- Base URL configuration.
- Custom reporter integration.

### Phase 5: POM Generation

1. **BasePage**: A shared class for common actions (click, type, wait).
2. **Page Objects**: Specific classes for each main page/section of the site (e.g., `LoginPage`, `DashboardPage`).

### Phase 6: Test Generation

Generates `.spec.ts` files that:
- Import Page Objects.
- Follow the Arrange-Act-Assert pattern.
- Use descriptive `test.step()` blocks for clean reporting.

## Best Practices Followed

- **Strict Locators**: Prefers `getByRole`, `getByText`, and `getByTestId`.
- **Automatic Waiting**: Leverages Playwright's built-in auto-waiting rather than hard sleeps.
- **Independence**: Each test is isolated and can run in parallel.
- **Reporting**: Configures custom reporters for better CI/CD visibility.
- **Clean Code**: Uses DRY (Don't Repeat Yourself) via the `utils` folder and shared components.

## Example Generated Structure

```text
playwright-automation/
├── playwright.config.ts
├── package.json
├── tsconfig.json
├── pages/
│   ├── BasePage.ts
│   ├── LoginPage.ts
│   └── DashboardPage.ts
├── tests/
│   └── auth.spec.ts
├── utils/
│   └── test-data.ts
└── reporters/
    └── custom-reporter.ts
```

## Quality Standards

The generated framework must:
1. Pass `npx tsc` if using TypeScript.
2. Be immediately runnable with `npx playwright test`.
3. Contain a `README.md` with clear execution and maintenance instructions.
4. Use environment variables for sensitive data like credentials.

---

## References

- **Playwright Official POM Guide**: https://playwright.dev/docs/pom
- **Best Practices**: https://playwright.dev/docs/best-practices
- **Custom Reporters**: https://playwright.dev/docs/test-reporters#custom-reporters

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/marufcode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
