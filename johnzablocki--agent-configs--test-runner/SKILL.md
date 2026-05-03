---
name: test-runner
description: Run test suite, identify failures, analyze root causes, and suggest fixes Use when this capability is needed.
metadata:
  author: johnzablocki
---

# Test Runner Skill

This skill runs your test suite, identifies test failures, analyzes root causes, and suggests or implements fixes to get tests passing.

## Objective

Execute the project's test suite (unit, integration, E2E), analyze any failures, diagnose root causes, and either fix the issues or provide detailed guidance on how to fix them.

## Execution Steps

### 1. Identify Test Framework

```bash
# Check package.json for test configuration
cat package.json | grep -A 5 "scripts"

# Look for test framework files
ls jest.config.* vitest.config.* playwright.config.* cypress.config.* .mocharc.*

# Check dependencies
npm ls | grep -E "(jest|vitest|mocha|jasmine|playwright|cypress)"
```

**Common frameworks:**
- **Jest:** Most popular for React/Node.js
- **Vitest:** Fast, Vite-native alternative to Jest
- **Mocha:** Traditional test framework
- **Playwright:** Modern E2E testing
- **Cypress:** Popular E2E testing
- **Jasmine:** BDD test framework

### 2. Run Test Suite

Execute tests based on framework:

```bash
# Via package.json script (preferred)
npm test
npm run test
yarn test

# Direct framework commands
npx jest
npx vitest run
npx playwright test
npx cypress run

# With coverage
npm test -- --coverage
npx jest --coverage

# Specific test file
npm test -- path/to/test.spec.ts
npx jest src/components/Button.test.tsx

# Watch mode (useful for fixing)
npm test -- --watch
npx jest --watch
```

### 3. Analyze Test Output

Parse test results for:

**Success Summary:**
```
Test Suites: 12 passed, 12 total
Tests:       45 passed, 45 total
Snapshots:   0 total
Time:        5.234 s
```

**Failure Summary:**
```
Test Suites: 2 failed, 10 passed, 12 total
Tests:       3 failed, 42 passed, 45 total
Snapshots:   0 total
Time:        5.678 s
```

**Individual Failures:**
Extract for each failure:
- Test name
- File path
- Expected vs actual results
- Error message
- Stack trace
- Line numbers

### 4. Categorize Test Failures

**Type 1: Assertion Failures**
```
expect(received).toBe(expected)

Expected: 5
Received: 4
```
**Cause:** Logic error, incorrect expected value, or business logic changed

**Type 2: Type Errors (TypeScript)**
```
TypeError: Cannot read property 'name' of undefined
```
**Cause:** Null/undefined value, missing mock data, API change

**Type 3: Timeout Errors**
```
Timeout - Async callback was not invoked within the 5000 ms timeout
```
**Cause:** Async operation didn't complete, missing mock, slow test

**Type 4: Mock/Stub Errors**
```
Expected mock function to have been called with: [arg1, arg2]
But it was called with: [arg1, arg3]
```
**Cause:** Function called with different arguments, implementation changed

**Type 5: Snapshot Mismatches**
```
Snapshot name: `Component renders correctly 1`

- Snapshot
+ Received

- <div className="old-class">
+ <div className="new-class">
```
**Cause:** UI changed (intentional or unintentional)

**Type 6: Environment Errors**
```
Cannot find module 'some-module' from 'test.ts'
```
**Cause:** Missing dependency, incorrect import path, config issue

### 5. Diagnose Root Cause

For each failure, investigate:

**Read the test file:**
```bash
# View the failing test
cat path/to/failing-test.spec.ts
```

**Read the implementation:**
```bash
# View the code being tested
cat path/to/implementation.ts
```

**Check recent changes:**
```bash
# See what changed recently
git log -p --all -- path/to/implementation.ts
git diff HEAD~1 -- path/to/implementation.ts
```

**Common root causes:**
1. **Code changed but test didn't update**
2. **Test is flaky (timing/race conditions)**
3. **Test expects wrong behavior**
4. **Missing or incorrect mock**
5. **Environment setup issue**
6. **Breaking change in dependency**

### 6. Fix Test Failures

Apply appropriate fixes based on root cause:

#### Fix Type 1: Update Assertion
```typescript
// Test failing because implementation changed
it('calculates total with tax', () => {
  const result = calculateTotal(100, 0.1);
  expect(result).toBe(110); // Was 105, now 110 after tax calc fix
});
```

#### Fix Type 2: Add Null Checks
```typescript
// Test failing with undefined error
it('displays user name', () => {
  const user = { id: 1, name: 'John' }; // Was undefined
  render(<UserCard user={user} />);
  expect(screen.getByText('John')).toBeInTheDocument();
});
```

#### Fix Type 3: Fix Async Handling
```typescript
// Test timing out
it('fetches user data', async () => {
  const promise = fetchUser(1);
  await promise; // Was missing await
  expect(mockFetch).toHaveBeenCalled();
});

// Or increase timeout for slow operations
it('processes large file', async () => {
  // ... test code
}, 10000); // 10 second timeout
```

#### Fix Type 4: Update Mocks
```typescript
// Mock not matching actual calls
const mockFn = jest.fn();
calculateDiscount({ total: 100, memberType: 'premium' }); // Added memberType

expect(mockFn).toHaveBeenCalledWith({
  total: 100,
  memberType: 'premium' // Update mock expectation
});
```

#### Fix Type 5: Update Snapshots
```bash
# If changes are intentional, update snapshots
npm test -- --updateSnapshot
npx jest --updateSnapshot

# Review changes carefully before committing
git diff path/to/__snapshots__/
```

#### Fix Type 6: Fix Imports/Config
```typescript
// Fix import path
// Old: import { helper } from '../utils/helper'
// New: import { helper } from '../../utils/helper'

// Or add missing mock
jest.mock('some-module', () => ({
  exportedFunction: jest.fn()
}));
```

### 7. Handle Flaky Tests

If tests pass/fail inconsistently:

**Common causes:**
```typescript
// BAD: Hard-coded dates
expect(user.createdAt).toBe('2024-02-14'); // Fails tomorrow

// GOOD: Relative or mocked dates
expect(user.createdAt).toBeInstanceOf(Date);

// BAD: Arbitrary timeouts
await wait(1000); // Might not be enough

// GOOD: Wait for specific condition
await waitFor(() => expect(screen.getByText('Loaded')).toBeInTheDocument());

// BAD: Test order dependency
let sharedData;
it('test 1', () => { sharedData = 'foo'; });
it('test 2', () => { expect(sharedData).toBe('foo'); }); // Fails if run alone

// GOOD: Independent tests
it('test 2', () => {
  const data = 'foo';
  expect(data).toBe('foo');
});
```

**Fixes for flaky tests:**
- Use proper async utilities (`waitFor`, `findBy`)
- Mock timers (`jest.useFakeTimers()`)
- Avoid shared state between tests
- Clean up after each test (`afterEach`)
- Use more robust selectors
- Avoid hardcoded timing

### 8. Run Tests Again

After fixes, verify:

```bash
# Run all tests
npm test

# Run only previously failing tests
npm test -- --testNamePattern="displays user name"
npm test -- path/to/previously-failing-test.spec.ts

# Run with coverage to see what's tested
npm test -- --coverage

# Run in watch mode while fixing
npm test -- --watch
```

### 9. Ensure Test Quality

**Check test coverage:**
```bash
npm test -- --coverage

# Coverage thresholds
# package.json or jest.config.js
{
  "jest": {
    "coverageThreshold": {
      "global": {
        "branches": 80,
        "functions": 80,
        "lines": 80,
        "statements": 80
      }
    }
  }
}
```

**Review test patterns:**
- Tests are descriptive and clear
- Tests are independent
- Tests use AAA pattern (Arrange, Act, Assert)
- Tests cover edge cases
- Tests are maintainable

### 10. Generate Test Report

```
🧪 Test Suite Results
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 SUMMARY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Test Suites: 12 passed, 12 total
Tests:       45 passed, 45 total
Time:        4.521 s

Coverage:
  Lines:      87.5%  (245/280)
  Branches:   82.3%  (89/108)
  Functions:  91.2%  (52/57)
  Statements: 86.8%  (243/280)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ TESTS FIXED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. ✓ UserCard displays user name
   Location: src/components/UserCard.test.tsx:15
   Issue: Test expected undefined user
   Fix: Added proper user mock object

2. ✓ calculateTotal with tax
   Location: src/utils/pricing.test.ts:42
   Issue: Expected value outdated after implementation change
   Fix: Updated expected value from 105 to 110

3. ✓ fetchUser data
   Location: src/api/users.test.ts:28
   Issue: Missing await on async operation
   Fix: Added await to promise

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📝 FILES MODIFIED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
src/components/UserCard.test.tsx
src/utils/pricing.test.ts
src/api/users.test.ts

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

💡 RECOMMENDATIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. Consider adding tests for new edge cases
2. Coverage below threshold in src/utils/validation.ts
3. Some tests take >1s, consider optimization
4. Update snapshots if UI changes are intentional

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Test Framework Specific Commands

### Jest
```bash
# Run all tests
npx jest

# Watch mode
npx jest --watch

# Coverage
npx jest --coverage

# Update snapshots
npx jest --updateSnapshot

# Run specific test
npx jest src/components/Button

# Verbose output
npx jest --verbose

# Clear cache
npx jest --clearCache
```

### Vitest
```bash
# Run tests
npx vitest run

# Watch mode
npx vitest

# UI mode
npx vitest --ui

# Coverage
npx vitest --coverage

# Run specific test
npx vitest run src/components/Button.test.ts
```

### Playwright
```bash
# Run all tests
npx playwright test

# Run in headed mode (see browser)
npx playwright test --headed

# Run specific test
npx playwright test tests/login.spec.ts

# Debug mode
npx playwright test --debug

# Run on specific browser
npx playwright test --project=chromium

# Generate report
npx playwright show-report
```

### Cypress
```bash
# Open Cypress UI
npx cypress open

# Run headless
npx cypress run

# Run specific test
npx cypress run --spec "cypress/e2e/login.cy.ts"

# Run on specific browser
npx cypress run --browser chrome
```

## Common Test Patterns & Fixes

### Pattern 1: Component Testing (React)
```typescript
import { render, screen, userEvent } from '@testing-library/react';

describe('LoginForm', () => {
  it('shows error on invalid credentials', async () => {
    const mockOnLogin = jest.fn().mockRejectedValue(new Error('Invalid'));

    render(<LoginForm onLogin={mockOnLogin} />);

    await userEvent.type(screen.getByLabelText(/email/i), 'test@example.com');
    await userEvent.type(screen.getByLabelText(/password/i), 'wrong');
    await userEvent.click(screen.getByRole('button', { name: /login/i }));

    expect(await screen.findByText(/invalid/i)).toBeInTheDocument();
  });
});
```

### Pattern 2: API Testing
```typescript
describe('User API', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  it('fetches user successfully', async () => {
    const mockUser = { id: 1, name: 'John' };
    global.fetch = jest.fn(() =>
      Promise.resolve({
        ok: true,
        json: () => Promise.resolve(mockUser),
      })
    );

    const user = await fetchUser(1);

    expect(user).toEqual(mockUser);
    expect(fetch).toHaveBeenCalledWith('/api/users/1');
  });
});
```

### Pattern 3: Async Testing
```typescript
// BAD: Test might pass even if promise rejects
it('fetches data', () => {
  fetchData().then(data => {
    expect(data).toBeDefined();
  });
});

// GOOD: Use async/await or return promise
it('fetches data', async () => {
  const data = await fetchData();
  expect(data).toBeDefined();
});
```

### Pattern 4: Error Handling
```typescript
it('handles errors gracefully', async () => {
  const mockError = new Error('Network error');
  jest.spyOn(api, 'fetchUser').mockRejectedValue(mockError);

  const result = await getUserData(1);

  expect(result.error).toBe('Network error');
  expect(result.data).toBeNull();
});
```

## Success Criteria

- All tests pass (100% pass rate)
- No flaky tests
- Test coverage meets thresholds
- Tests run in reasonable time (< 30s for unit tests)
- All fixes maintain test integrity
- Implementation code unchanged (unless buggy)

## Best Practices

### DO:
- ✅ Test behavior, not implementation
- ✅ Use descriptive test names
- ✅ Follow AAA pattern (Arrange, Act, Assert)
- ✅ Make tests independent
- ✅ Clean up after tests (afterEach)
- ✅ Mock external dependencies
- ✅ Test edge cases and error conditions
- ✅ Keep tests simple and focused

### DON'T:
- ❌ Test implementation details
- ❌ Have test dependencies (test order matters)
- ❌ Use arbitrary timeouts
- ❌ Test third-party code
- ❌ Write overly complex tests
- ❌ Skip tests (unless temporarily debugging)
- ❌ Ignore flaky tests
- ❌ Test everything in E2E (use test pyramid)

## Debugging Tests

```bash
# Run single test in debug mode
node --inspect-brk node_modules/.bin/jest --runInBand path/to/test.spec.ts

# Increase verbosity
npm test -- --verbose

# Show full diff for failures
npm test -- --expand

# Disable test coverage (faster)
npm test -- --coverage=false

# Run tests serially (helps identify shared state issues)
npm test -- --runInBand
```

## CI/CD Integration

```yaml
# GitHub Actions example
test:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-node@v4
    - run: npm ci
    - run: npm test -- --coverage
    - uses: codecov/codecov-action@v3
      with:
        files: ./coverage/lcov.info
```

## Notes

- Fix tests before merging to prevent broken main branch
- Understand WHY a test failed before fixing it
- If test is wrong, fix the test; if implementation is wrong, fix the code
- Don't delete failing tests without understanding them
- Flaky tests are worse than no tests - fix or remove them
- Run tests locally before pushing to CI
- Keep test execution time reasonable (parallelize if needed)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/johnzablocki) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
