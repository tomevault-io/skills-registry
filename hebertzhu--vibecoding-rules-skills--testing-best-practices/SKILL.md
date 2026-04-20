---
name: testing-best-practices
description: Comprehensive testing standards including TDD methodology, 80% coverage requirements, unit/integration/E2E testing patterns, and test quality guidelines. Use when writing tests, implementing TDD, checking coverage, debugging test failures, or when asked about testing standards, test-driven development, or quality assurance. Use when this capability is needed.
metadata:
  author: hebertzhu
---

# Testing Best Practices

Complete testing methodology with TDD workflow, coverage standards, and quality guidelines.

## Quick Start

### Minimum Coverage Requirements

- **Statements**: ≥ 80%
- **Branches**: ≥ 75%
- **Functions**: ≥ 80%
- **Lines**: ≥ 80%

### Check Coverage

```bash
npm run test -- --coverage
```

## TDD Workflow (MANDATORY)

Follow the RED-GREEN-REFACTOR cycle:

```
1. RED    → Write test, test fails
2. GREEN  → Write code, test passes
3. REFACTOR → Improve code, tests still pass
4. VERIFY → Confirm coverage ≥ 80%
```

### Step-by-Step TDD

#### 1. Write Test First (RED)

```typescript
// Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react'
import { Button } from './Button'

describe('Button', () => {
  it('should render button text', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByRole('button')).toHaveTextContent('Click me')
  })

  it('should handle click events', () => {
    const handleClick = jest.fn()
    render(<Button onClick={handleClick}>Click</Button>)
    
    fireEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalledTimes(1)
  })
})
```

#### 2. Run Test - Should Fail (RED)

```bash
npm run test
# Expected: FAIL - Button component doesn't exist
```

**If test passes, the test is wrong!**

#### 3. Write Minimal Implementation (GREEN)

```typescript
// Button.tsx
interface ButtonProps {
  children: React.ReactNode
  onClick?: () => void
}

export const Button: React.FC<ButtonProps> = ({ children, onClick }) => {
  return <button onClick={onClick}>{children}</button>
}
```

#### 4. Run Test - Should Pass (GREEN)

```bash
npm run test
# Expected: PASS - All tests pass
```

#### 5. Refactor (REFACTOR)

Improve code quality while keeping tests green:

```typescript
interface ButtonProps {
  children: React.ReactNode
  onClick?: () => void
  variant?: 'primary' | 'secondary'
  disabled?: boolean
}

export const Button: React.FC<ButtonProps> = ({ 
  children, 
  onClick,
  variant = 'primary',
  disabled = false
}) => {
  return (
    <button 
      onClick={onClick}
      disabled={disabled}
      className={`button button--${variant}`}
    >
      {children}
    </button>
  )
}
```

#### 6. Verify Coverage (VERIFY)

```bash
npm run test -- --coverage
# Ensure coverage ≥ 80%
```

## Test Types

### 1. Unit Tests

Test isolated functions and components.

**Example**:
```typescript
describe('formatDate', () => {
  it('should format date correctly', () => {
    const date = new Date('2025-01-23')
    expect(formatDate(date)).toBe('2025-01-23')
  })

  it('should handle invalid dates', () => {
    expect(formatDate(null)).toBe('')
  })
})
```

**Tools**: Vitest, Jest, React Testing Library

### 2. Integration Tests

Test module interactions.

**Example**:
```typescript
describe('User API', () => {
  it('should create and fetch user', async () => {
    const user = await createUser({ name: 'Test User' })
    expect(user.id).toBeDefined()
    
    const fetchedUser = await getUser(user.id)
    expect(fetchedUser.name).toBe('Test User')
  })
})
```

**Tools**: Supertest, MSW

### 3. E2E Tests

Test complete user flows.

**Example**:
```typescript
test('user login flow', async ({ page }) => {
  await page.goto('/')
  await page.click('text=Login')
  await page.fill('[name="email"]', 'test@example.com')
  await page.fill('[name="password"]', 'password123')
  await page.click('button[type="submit"]')
  
  await expect(page.locator('text=Welcome back')).toBeVisible()
})
```

**Tools**: Playwright

## Test Quality Standards

### Naming Convention

```typescript
// ❌ Bad
test('test1', () => {})
test('works', () => {})

// ✅ Good
test('should call onClick handler when user clicks button', () => {})
test('should display error message when input is invalid', () => {})
```

### AAA Pattern

**Arrange - Act - Assert**

```typescript
test('should calculate total price', () => {
  // Arrange: Prepare test data
  const items = [
    { price: 10, quantity: 2 },
    { price: 5, quantity: 3 }
  ]
  
  // Act: Execute operation
  const total = calculateTotal(items)
  
  // Assert: Verify result
  expect(total).toBe(35)
})
```

### One Assertion Per Test

```typescript
// ❌ Bad: Testing too many things
test('user functionality', () => {
  expect(user.name).toBe('Test')
  expect(user.email).toBe('test@example.com')
  expect(user.isActive).toBe(true)
  expect(user.role).toBe('admin')
})

// ✅ Good: Each test focuses on one aspect
test('should have correct username', () => {
  expect(user.name).toBe('Test')
})

test('should have correct email', () => {
  expect(user.email).toBe('test@example.com')
})
```

### Test Edge Cases

```typescript
describe('divide', () => {
  it('should divide correctly', () => {
    expect(divide(10, 2)).toBe(5)
  })

  it('should handle division by zero', () => {
    expect(() => divide(10, 0)).toThrow('Cannot divide by zero')
  })

  it('should handle negative numbers', () => {
    expect(divide(-10, 2)).toBe(-5)
  })

  it('should handle decimals', () => {
    expect(divide(10, 3)).toBeCloseTo(3.33, 2)
  })
})
```

## Common Testing Pitfalls

### 1. Test Isolation Issues

```typescript
// ❌ Wrong: Tests share state
let sharedState = {}

test('test 1', () => {
  sharedState.value = 1
  expect(sharedState.value).toBe(1)
})

test('test 2', () => {
  // Depends on test 1 state - Bad!
  expect(sharedState.value).toBe(1)
})

// ✅ Correct: Each test is independent
test('test 1', () => {
  const state = { value: 1 }
  expect(state.value).toBe(1)
})

test('test 2', () => {
  const state = { value: 2 }
  expect(state.value).toBe(2)
})
```

### 2. Incorrect Mocking

```typescript
// ❌ Wrong: Mock not properly set up
jest.mock('./api')
// Forgot to define mock return value

// ✅ Correct: Complete mock setup
jest.mock('./api')
import { fetchUser } from './api'

const mockFetchUser = fetchUser as jest.MockedFunction<typeof fetchUser>
mockFetchUser.mockResolvedValue({ id: 1, name: 'Test' })
```

### 3. Async Test Issues

```typescript
// ❌ Wrong: Not waiting for async operation
test('async test', () => {
  fetchData().then(data => {
    expect(data).toBeDefined()
  })
  // Test ends before Promise completes
})

// ✅ Correct: Using async/await
test('async test', async () => {
  const data = await fetchData()
  expect(data).toBeDefined()
})
```

## Test Commands

```bash
# Run all tests
npm run test

# Run specific file
npm run test -- path/to/test.spec.ts

# Watch mode (development)
npm run test -- --watch

# Generate coverage report
npm run test -- --coverage

# Run E2E tests
npm run test:e2e

# Debug tests
npm run test -- --inspect-brk
```

## Fixing Principle

**IMPORTANT**: Fix implementation, not tests (unless test itself is wrong)

```
If test fails:
1. First check if test is correct
2. If test is correct, fix implementation code
3. Don't modify test just to make it pass
4. Tests should reflect real requirements
```

## Coverage Report Interpretation

```
Statements   : 85.5% ( 342/400 )  ✅ Pass
Branches     : 78.2% ( 156/200 )  ✅ Pass
Functions    : 82.1% ( 164/200 )  ✅ Pass
Lines        : 84.8% ( 339/400 )  ✅ Pass
```

### Improving Coverage

1. **Identify uncovered branches**
2. **Add tests for edge cases**
3. **Test error handling paths**
4. **Remove dead code**

## Pre-Commit Checklist

### Test Writing
- [ ] All new features have unit tests
- [ ] Key features have integration tests
- [ ] Core flows have E2E tests
- [ ] Edge cases are tested
- [ ] Error handling is tested

### Test Quality
- [ ] Test names are descriptive
- [ ] Follow AAA pattern
- [ ] Tests are independent
- [ ] Mocks are used correctly
- [ ] Async tests handled properly

### Coverage
- [ ] Statement coverage ≥ 80%
- [ ] Branch coverage ≥ 75%
- [ ] Function coverage ≥ 80%
- [ ] Line coverage ≥ 80%

### Test Execution
- [ ] All tests pass
- [ ] No skipped tests
- [ ] No flaky tests
- [ ] Tests run reasonably fast

## Best Practices Summary

1. **Test First** - Follow TDD, write tests before code
2. **High Coverage** - Maintain 80%+ test coverage
3. **Fast Feedback** - Tests should run quickly
4. **Independent Tests** - Each test should be independent
5. **Clear Naming** - Test names should clearly describe what they test
6. **Edge Cases** - Don't just test happy path
7. **Continuous Improvement** - Regularly review and improve test suite

---

*Remember: Tests are not a burden, they are confidence. Good tests let you refactor, improve, and innovate fearlessly.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hebertzhu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
