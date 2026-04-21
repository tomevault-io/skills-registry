---
name: analyze-code-quality
description: Analyze code quality including ESLint, Prettier, tests, TypeScript, and NestJS best practices. Use when reviewing code quality, checking test coverage, or auditing for best practices. Use when this capability is needed.
metadata:
  author: jovicon
---

Perform comprehensive code quality analysis.

**Module:** $0 (if empty, analyze entire project)
**Focus area:** $1 (options: eslint, prettier, tests, typescript, nestjs, all - default: all)

## Analysis Scope

The analysis will cover the specified focus area or complete code quality review if "all" is selected.

### 1. ESLint Analysis

**A. Configuration Validation**

- ✅ Check `.eslintrc.js` exists and is properly configured
- ✅ Verify Airbnb style guide is enabled
- ✅ Check TypeScript ESLint parser configuration
- ✅ Validate custom rules alignment with project needs
- ✅ Verify ignore patterns (.eslintignore)

**B. Code Violations Detection**
Run ESLint analysis and categorize issues:

```bash
npm run lint
```

**Report violations by severity:**

- 🔴 **Errors** (must fix)
- 🟡 **Warnings** (should fix)
- 🔵 **Info** (nice to fix)

**Common violations to check:**

- ❌ Unused variables and imports
- ❌ Console.log statements in production code
- ❌ Missing return types on functions
- ❌ Any type usage (should use specific types)
- ❌ Inconsistent naming conventions
- ❌ Unreachable code
- ❌ Debugger statements
- ❌ Empty functions/blocks
- ❌ Complexity violations (cyclomatic complexity > 10)
- ❌ Max line length violations (> 120 chars)
- ❌ Missing JSDoc for public APIs

---

### 2. Prettier Formatting Analysis

**A. Configuration Check**

- ✅ Verify `.prettierrc` exists
- ✅ Check configuration settings
- ✅ Verify `.prettierignore` configuration

**B. Formatting Issues Detection**
Run Prettier check:

```bash
npm run format:check
# or
npx prettier --check "src/**/*.ts"
```

**Report:**

- List all files with formatting issues
- Count total files needing formatting
- Show sample formatting differences
- Provide auto-fix command: `npm run format`

---

### 3. Unit Test Coverage Analysis

**A. Test Execution**
Run tests and collect metrics:

```bash
npm run test:cov
```

**B. Coverage Metrics Analysis**

**Overall Coverage:**

- ✅ Statements coverage (target: >80%)
- ✅ Branches coverage (target: >75%)
- ✅ Functions coverage (target: >80%)
- ✅ Lines coverage (target: >80%)

**C. Test Quality Checks**

**File Coverage:**

- ❌ List files WITHOUT test files (.spec.ts)
- ✅ Verify test file naming convention matches source files
- ❌ Find test files with low assertions
- ❌ Find skipped/disabled tests (describe.skip, it.skip)
- ❌ Find focused tests (fit, fdescribe) - should never be committed

**Test Patterns:**

- ✅ Check use cases have corresponding tests
- ✅ Verify domain entities have unit tests
- ✅ Check value objects have validation tests
- ✅ Verify event handlers have tests
- ✅ Check repository adapters have integration tests
- ❌ Flag missing edge case tests
- ❌ Flag missing error case tests

**D. Test Configuration**

- ✅ Verify jest.config.js configuration
- ✅ Check test environment setup
- ✅ Validate code coverage thresholds
- ✅ Check SonarQube reporter configuration
- ✅ Verify path aliases work in tests

---

### 4. TypeScript Analysis

**A. Configuration Validation**
Check `tsconfig.json` settings for strict mode and best practices

**B. Type Safety Issues**

Run TypeScript compiler check:

```bash
npx tsc --noEmit
```

**Flag common issues:**

- ❌ `any` type usage (use specific types or `unknown`)
- ❌ `@ts-ignore` comments (fix the issue instead)
- ❌ `as any` type assertions (use proper typing)
- ❌ Non-null assertions (`!`) without justification
- ❌ Implicit any types
- ❌ Missing return types on functions
- ❌ Unsafe type assertions
- ❌ Unused variables/imports
- ❌ Type vs Interface usage (prefer interface for object shapes)

**C. TypeScript Best Practices**

```typescript
// ✅ GOOD
interface CreateOrderDTO {
  customerId: string;
  items: OrderItem[];
}

function createOrder(dto: CreateOrderDTO): Promise<Result<Order>> {
  // ...
}

// ❌ BAD
function createOrder(dto: any): Promise<any> {
  // ...
}
```

---

### 5. NestJS Best Practices Analysis

**A. Module Organization**

**Check module structure:**

- ✅ Each module has proper `@Module()` decorator
- ✅ Providers array contains all injectables
- ✅ Imports/Exports are correctly configured
- ✅ No circular dependencies between modules
- ❌ Flag modules importing themselves
- ❌ Flag overly large modules (>10 providers)

**B. Dependency Injection**

**Proper DI patterns:**

```typescript
// ✅ GOOD - Constructor injection with types
@Injectable()
export class OrderService {
  constructor(
    @Inject('IOrderRepository') private readonly orderRepo: IOrderRepository,
    private readonly logger: Logger,
  ) {}
}

// ❌ BAD - No injection, direct instantiation
export class OrderService {
  private orderRepo = new OrderRepository(); // Don't do this!
}
```

**C. Decorator Usage**

**Controllers:**

- ✅ Proper `@Controller()` with route prefix
- ✅ HTTP method decorators (`@Get()`, `@Post()`, etc.)
- ✅ `@Body()`, `@Param()`, `@Query()` decorators
- ✅ Proper DTO validation with class-validator
- ❌ Missing `@ApiTags()` for Swagger documentation
- ❌ Missing response type decorators (`@ApiResponse()`)

**Services:**

- ✅ `@Injectable()` on all services
- ✅ Proper scope (singleton, request, transient)
- ❌ Missing `@Injectable()` decorator

**Event Handlers:**

- ✅ `@OnEvent()` decorator with proper event name
- ✅ Async handlers return Promise<void>
- ❌ Synchronous long-running handlers (should be async)

**D. NestJS-Specific Patterns**

**Guards, Interceptors, Pipes, Filters:**

- ✅ Implement proper interfaces
- ✅ Proper use of decorators
- ✅ Appropriate placement (global vs route-specific)

**E. Performance & Best Practices**

- ❌ Synchronous operations blocking event loop
- ❌ Missing `@UseInterceptors()` for logging/transformation
- ❌ Direct database queries in controllers (should use services)
- ❌ Business logic in controllers (should be in use cases/domain)
- ❌ Missing validation pipes on endpoints
- ❌ Improper exception handling
- ❌ Memory leaks (unsubscribed observables)
- ❌ N+1 query problems

---

### 6. Code Complexity Analysis

**A. Cyclomatic Complexity**

- ✅ Functions with complexity score > 10 (refactor needed)
- ✅ Classes with too many methods (> 20)
- ❌ Deep nesting levels (> 4)

**B. Code Smells**

- ❌ **Long Methods** - Methods > 50 lines
- ❌ **Long Parameter Lists** - Functions with > 4 parameters
- ❌ **Large Classes** - Classes > 300 lines
- ❌ **Duplicate Code** - Similar code blocks
- ❌ **Dead Code** - Unused exports/functions
- ❌ **Magic Numbers** - Hardcoded numbers without constants
- ❌ **Long Conditional Chains** - Multiple if-else if-else

**C. Maintainability Metrics**

- Comment density (target: 10-20%)
- File length distribution
- Import complexity (too many imports = high coupling)

---

### 7. Dependency & Security Analysis

**A. Package.json Review**

- ✅ Check for outdated dependencies: `npm outdated`
- ✅ Check for security vulnerabilities: `npm audit`
- ✅ Verify no dev dependencies in production code
- ❌ Flag deprecated packages
- ❌ Flag packages with known vulnerabilities

**B. Import Analysis**

- ✅ Verify path aliases work (@shared, @modules)
- ❌ Flag relative imports going up many levels (../../../)
- ❌ Flag barrel file anti-patterns (index.ts exporting everything)
- ❌ Flag circular dependencies

---

### 8. Git & Commit Quality

**A. Pre-commit Hooks**

- ✅ Verify Lefthook is configured
- ✅ Check pre-commit runs linting
- ✅ Check pre-commit runs formatting
- ✅ Verify tests run before push

**B. Code Review Checklist**

- ❌ Large commits (> 500 lines)
- ❌ Commits mixing multiple concerns
- ❌ Missing commit messages or poor messages
- ❌ Direct commits to main/master

---

## Output Format

Provide a comprehensive report:

### 📊 Code Quality Score

- **Overall Score:** 0-100%
- **ESLint:** Errors, Warnings, Info counts
- **Prettier:** Files needing formatting
- **Test Coverage:** % by category
- **TypeScript:** Type safety score
- **NestJS:** Best practices compliance

### ✅ Strengths

- Well-tested modules
- Good type coverage
- Clean formatting
- Proper DI usage

### ⚠️ Critical Issues (Fix Immediately)

For each issue:

- **Category:** ESLint / Testing / TypeScript / NestJS
- **Severity:** Critical / High / Medium / Low
- **Location:** File:Line
- **Issue:** Description
- **Impact:** Why it matters
- **Fix:** How to resolve
- **Command:** Auto-fix command if available

### 🔧 Warnings (Should Fix)

Non-critical but important issues

### 💡 Recommendations

- Suggested improvements
- Refactoring opportunities
- Performance optimizations

### 📈 Metrics Summary

```
Code Quality Metrics:
├── ESLint Issues: 23 (12 errors, 11 warnings)
├── Prettier: 5 files need formatting
├── Test Coverage: 78% (target: 80%)
├── TypeScript Errors: 0
├── Unused Exports: 8
├── Code Complexity: 3 functions > 10
├── Security Vulnerabilities: 2 (1 high, 1 medium)
└── Outdated Packages: 5
```

### 🎯 Action Items (Prioritized)

1. **Critical:** Fix ESLint errors preventing build
2. **High:** Increase test coverage for user module (45% → 80%)
3. **High:** Fix security vulnerability in package X
4. **Medium:** Remove 8 unused exports
5. **Medium:** Format 5 files with Prettier
6. **Low:** Update 5 outdated packages

### 🚀 Quick Fixes

Auto-fixable issues with commands:

```bash
# Fix ESLint issues
npm run lint:fix

# Format code
npm run format

# Update outdated packages
npm update

# Fix security issues
npm audit fix
```

---

## Analysis Guidelines

- Run actual linting/testing commands to get real data
- Provide file paths and line numbers for all issues
- Include code snippets showing violations
- Suggest concrete fixes with examples
- Prioritize issues by impact and effort
- Consider project context (not all warnings are critical)
- Provide both quick wins and long-term improvements
- Include metrics and trends if analyzing over time

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jovicon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
