---
name: test-coverage-auditor
description: This skill activates when: Use when this capability is needed.
metadata:
  author: gregsuptown
---
---
name: test-coverage-auditor
description: Verify test coverage and quality for new features. Ensures service functions have unit tests, tRPC procedures have integration tests, and critical paths are covered before deployment.
allowed-tools: Read, Grep, Glob, Bash
---

# Test Coverage Auditor

## Purpose

Ensures adequate test coverage before deployment by:
- Verifying service functions have corresponding tests
- Checking tRPC procedures have integration tests
- Analyzing coverage reports for gaps
- Flagging untested critical paths
- Suggesting test file creation

## Auto-Invocation Triggers

This skill activates when:
- User asks to "check test coverage" or "verify tests"
- Before deployment to production
- When new service functions are added to `server/services/`
- When new tRPC procedures are added to routers
- User mentions "tests" or "coverage"
- Before creating a pull request

## Coverage Standards

### Service Layer (CRITICAL)
All functions in `server/services/` MUST have unit tests:
- ✅ Happy path tests
- ✅ Error handling tests
- ✅ Edge case tests
- ✅ Input validation tests
- Target: 80%+ coverage

### tRPC Procedures (HIGH PRIORITY)
All tRPC procedures SHOULD have integration tests:
- ✅ Input validation tests (Zod schemas)
- ✅ Authentication tests (protected procedures)
- ✅ Authorization tests (ownership/permissions)
- ✅ Error response tests
- Target: 70%+ coverage

### React Components (MEDIUM PRIORITY)
Critical components SHOULD have tests:
- ✅ Snapshot tests for UI consistency
- ✅ Interaction tests (button clicks, form submissions)
- ✅ Hook tests (custom hooks)
- Target: 60%+ coverage

### Utility Functions (MEDIUM PRIORITY)
All utility functions in `lib/` or `utils/` SHOULD have tests:
- ✅ Pure function tests
- ✅ Edge cases
- Target: 80%+ coverage

## Audit Process

### Step 1: Identify Service Functions
```bash
# Find all service files
find server/services -name "*.ts" -not -name "*.test.ts"
```

For each service file:
1. Read the file
2. Extract exported functions
3. Check for corresponding `.test.ts` file
4. Flag if missing

### Step 2: Check Test File Existence

**Expected Pattern:**
```
server/services/user.ts        → server/services/user.test.ts
server/services/project.ts     → server/services/project.test.ts
server/services/ai-assistant.ts → server/services/ai-assistant.test.ts
```

### Step 3: Analyze Test Quality

For each existing test file, check:
- ✅ Tests exported functions
- ✅ Tests error cases
- ✅ Uses proper assertions
- ✅ Mocks external dependencies
- ✅ No skipped tests without issue reference

### Step 4: Run Coverage Report (Optional)

If coverage tool is available:
```bash
npm run test:coverage
```

Parse output for:
- Overall coverage percentage
- Uncovered lines
- Uncovered functions
- Coverage by file

### Step 5: Check tRPC Procedures

```bash
# Find all router files
find server -name "*router*.ts" -o -name "routers.ts"
```

For each router:
1. Extract procedure definitions
2. Check for integration tests
3. Flag if missing

## Output Format

### All Tests Present
```
✅ Test Coverage Audit Passed

Service Layer:
✅ server/services/user.ts (user.test.ts exists)
✅ server/services/project.ts (project.test.ts exists)
✅ server/services/hubspot.ts (hubspot.test.ts exists)
✅ server/services/ai-assistant.ts (ai-assistant.test.ts exists)

tRPC Procedures:
✅ server/routers.ts (integration tests in routers.test.ts)
✅ server/admin-router.ts (integration tests in admin-router.test.ts)

Coverage: 85% (target: 80%)
Status: Ready for deployment ✅
```

### Tests Missing
```
⚠️  Test Coverage Issues Found

Missing Tests (4):

Service Layer (CRITICAL):
❌ server/services/analytics.ts
   No test file found
   Create: server/services/analytics.test.ts
   Functions to test:
   - calculateRevenue()
   - getConversionRate()
   - getProjectTrends()

❌ server/services/email.ts
   No test file found
   Create: server/services/email.test.ts
   Functions to test:
   - sendEmail()
   - sendProjectUpdate()
   - sendReceipt()

tRPC Procedures (HIGH):
⚠️  server/admin-router.ts
   No integration tests found
   Create: server/admin-router.test.ts

React Components (MEDIUM):
⚠️  client/src/pages/AdminAnalytics.tsx
   Complex component with no tests
   Create: client/src/pages/AdminAnalytics.test.tsx

Recommendation: Add tests before deployment
Priority: CRITICAL (2 service files untested)
```

### With Coverage Report
```
📊 Test Coverage Report

Overall Coverage: 67% (target: 80%) ⚠️

By Category:
✅ Service Layer: 82% (12/15 functions)
⚠️  tRPC Procedures: 65% (13/20 procedures)
⚠️  React Components: 45% (18/40 components)
✅ Utility Functions: 88% (22/25 functions)

Uncovered Critical Paths (3):

1. server/services/payment.ts:45-67
   Function: processRefund()
   Risk: HIGH (financial transactions)
   Lines: 23 uncovered

2. server/services/user.ts:123-145
   Function: deleteUserData()
   Risk: HIGH (data deletion)
   Lines: 23 uncovered

3. server/routers.ts:234-256
   Procedure: createProject
   Risk: MEDIUM (business logic)
   Lines: 23 uncovered

Action Required: Add tests for 3 critical paths
```

## Check Criteria

### CRITICAL (Must Have Tests)
- ❌ Payment processing functions
- ❌ User data deletion functions
- ❌ Authentication/authorization logic
- ❌ Data migration functions
- ❌ Financial calculations

### HIGH PRIORITY (Should Have Tests)
- ⚠️  All service layer functions
- ⚠️  Protected tRPC procedures
- ⚠️  Database operations (CRUD)
- ⚠️  External API integrations

### MEDIUM PRIORITY (Nice to Have)
- ℹ️  React components with complex logic
- ℹ️  Custom hooks
- ℹ️  Utility functions
- ℹ️  Public tRPC procedures

### LOW PRIORITY (Optional)
- Simple presentational components
- Type definitions
- Constants/configuration files

## Test Template Generation

### Service Function Test Template
```typescript
import { describe, it, expect, vi } from 'vitest';
import { functionName } from './service-name';

describe('functionName', () => {
  it('should handle happy path', async () => {
    // Arrange
    const input = { /* test data */ };

    // Act
    const result = await functionName(input);

    // Assert
    expect(result).toEqual({ /* expected output */ });
  });

  it('should handle error case', async () => {
    // Arrange
    const invalidInput = { /* invalid data */ };

    // Act & Assert
    await expect(functionName(invalidInput)).rejects.toThrow('Expected error');
  });

  it('should validate input', async () => {
    // Test edge cases, null, undefined, etc.
  });
});
```

### tRPC Procedure Test Template
```typescript
import { describe, it, expect } from 'vitest';
import { createCaller } from '../__tests__/helpers';

describe('procedureName', () => {
  it('should require authentication', async () => {
    const caller = createCaller({ user: null });

    await expect(
      caller.procedureName({ id: '123' })
    ).rejects.toThrow('UNAUTHORIZED');
  });

  it('should validate input', async () => {
    const caller = createCaller({ user: mockUser });

    await expect(
      caller.procedureName({ invalid: 'data' })
    ).rejects.toThrow('Validation error');
  });

  it('should process valid request', async () => {
    const caller = createCaller({ user: mockUser });

    const result = await caller.procedureName({ id: '123' });

    expect(result).toBeDefined();
  });
});
```

## Examples

### Example 1: New Service Without Tests
**Scenario:** Developer added `server/services/analytics.ts` with 5 functions

**Audit Output:**
```
❌ CRITICAL: Missing Tests

File: server/services/analytics.ts
Functions (5):
- calculateRevenue() - 23 lines
- getConversionRate() - 15 lines
- getProjectTrends() - 34 lines
- getTopProducts() - 18 lines
- getRevenueByMonth() - 27 lines

Total: 117 lines of untested code

Action Required:
1. Create server/services/analytics.test.ts
2. Add tests for all 5 functions
3. Aim for 80%+ coverage

Priority: CRITICAL - Do not deploy without tests
```

### Example 2: Partial Coverage
**Scenario:** Service has tests but coverage is low

**Audit Output:**
```
⚠️  LOW COVERAGE: server/services/project.ts

Current Coverage: 45% (9/20 functions tested)

Tested Functions (9):
✅ getById()
✅ create()
✅ update()
...

Untested Functions (11):
❌ delete() - HIGH RISK (data deletion)
❌ archive()
❌ restore()
...

Recommendation:
1. Prioritize testing delete() (high risk)
2. Add tests for archive() and restore()
3. Target: 80% coverage

Current test file: server/services/project.test.ts
Add 11 more test cases
```

### Example 3: All Tests Present
**Scenario:** Complete test coverage

**Audit Output:**
```
✅ Excellent Test Coverage

Service Layer: 15/15 files tested (100%)
tRPC Procedures: 18/20 tested (90%)
React Components: 28/40 tested (70%)

Overall Coverage: 87%

Highlights:
✅ All critical paths tested
✅ Error handling covered
✅ Edge cases tested
✅ Integration tests present

Status: Ready for production deployment! 🚀
```

## Integration

### package.json Scripts
Add these scripts:
```json
{
  "scripts": {
    "test": "vitest",
    "test:coverage": "vitest --coverage",
    "test:watch": "vitest --watch",
    "test:ui": "vitest --ui"
  }
}
```

### CI/CD Integration
Add to GitHub Actions:
```yaml
- name: Run Tests
  run: npm test

- name: Check Coverage
  run: |
    npm run test:coverage
    # Fail if coverage below 80%
```

### Pre-Deployment Checklist
Always check coverage before deploying:
```bash
npm run test:coverage
# Review coverage report
# Add tests if below threshold
```

## Configuration

### Coverage Thresholds
Configure in `vitest.config.ts`:
```typescript
export default {
  test: {
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      lines: 80,
      functions: 80,
      branches: 75,
      statements: 80
    }
  }
}
```

### Excluded Files
Skip these from coverage requirements:
- `*.test.ts`, `*.spec.ts`
- `__tests__/`
- `node_modules/`
- `dist/`, `build/`
- `*.config.ts`
- Type definition files

## Best Practices

### Test Naming
```typescript
// ✅ GOOD - Descriptive
describe('UserService.create', () => {
  it('should create user with valid data', async () => {});
  it('should throw error when email already exists', async () => {});
  it('should hash password before storing', async () => {});
});

// ❌ BAD - Vague
describe('user', () => {
  it('works', () => {});
  it('test 2', () => {});
});
```

### Test Organization
```
server/services/
  user.ts
  user.test.ts          ← Same directory
  project.ts
  project.test.ts

__tests__/
  helpers.ts            ← Shared test utilities
  setup.ts              ← Test configuration
```

### Mocking
```typescript
// ✅ GOOD - Mock external dependencies
vi.mock('./database', () => ({
  db: {
    query: {
      users: {
        findFirst: vi.fn()
      }
    }
  }
}));

// Test uses the mock
it('should fetch user', async () => {
  db.query.users.findFirst.mockResolvedValue(mockUser);
  // ... test code
});
```

## Maintenance

### Weekly
- Review test coverage trends
- Add tests for new features
- Fix failing tests immediately

### Monthly
- Review coverage report for gaps
- Refactor tests for clarity
- Update test utilities

### Quarterly
- Audit test quality
- Remove obsolete tests
- Update testing patterns

## Resources

- **Vitest Docs:** https://vitest.dev/
- **Testing Library:** https://testing-library.com/
- **tRPC Testing:** https://trpc.io/docs/server/testing

---

This skill ensures code quality through comprehensive test coverage.
Untested code is a risk - catch it before production!

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gregsuptown) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
