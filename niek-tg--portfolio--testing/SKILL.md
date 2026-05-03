---
name: testing
description: Guide for writing and maintaining tests in this Astro portfolio project. Use this when asked to write tests, fix failing tests, or improve test coverage. Use when this capability is needed.
metadata:
  author: niek-tg
---

# Testing Skill

This skill provides guidelines for writing and maintaining tests in the portfolio project using Vitest.

## Testing Framework

This project uses:
- **Vitest** for unit testing
- **@vitest/ui** for test visualization
- **@vitest/coverage-v8** for coverage reports

## Test Location

Tests must be co-located with source files:

```
✓ Correct:
src/utils/helpers.ts
src/utils/helpers.test.ts

✗ Incorrect:
src/utils/helpers.ts
src/__tests__/helpers.test.ts
```

## File Naming

- Test files: `{filename}.test.ts` (e.g., `helpers.test.ts`)
- Keep the same name as the source file being tested
- Use relative imports: `import { fn } from './module'`

## Test Structure

Follow the Arrange-Act-Assert pattern:

```typescript
import { describe, it, expect } from 'vitest';
import { functionToTest } from './module';

describe('functionToTest', () => {
  it('should do something specific', () => {
    // Arrange - Set up test data
    const input = 'test';
    
    // Act - Call the function
    const result = functionToTest(input);
    
    // Assert - Verify the result
    expect(result).toBe('expected');
  });
});
```

## Testing Guidelines

### Test Names
- Use descriptive names that explain what is being tested
- Start with "should" to describe expected behavior
- Be specific about the scenario being tested

**Good:**
```typescript
it('should return empty array when input is null', () => {})
it('should format date as YYYY-MM-DD', () => {})
it('should throw error when required field is missing', () => {})
```

**Bad:**
```typescript
it('works', () => {})
it('test case 1', () => {})
it('returns data', () => {})
```

### One Assertion Per Test
- Each test should verify one logical assertion
- Makes failures easier to diagnose
- Tests serve as documentation

**Good:**
```typescript
it('should return lowercase email', () => {
  const result = normalizeEmail('TEST@EXAMPLE.COM');
  expect(result).toBe('test@example.com');
});

it('should trim whitespace from email', () => {
  const result = normalizeEmail('  test@example.com  ');
  expect(result).toBe('test@example.com');
});
```

**Bad:**
```typescript
it('should normalize email', () => {
  expect(normalizeEmail('TEST@EXAMPLE.COM')).toBe('test@example.com');
  expect(normalizeEmail('  test@example.com  ')).toBe('test@example.com');
  expect(normalizeEmail('Test@Example.Com')).toBe('test@example.com');
});
```

### Use Describe Blocks
- Group related tests together
- Nest describe blocks for different scenarios
- Makes test output more readable

```typescript
describe('formatDate', () => {
  describe('with valid date', () => {
    it('should format as MM/DD/YYYY', () => {});
    it('should handle single-digit months', () => {});
  });
  
  describe('with invalid date', () => {
    it('should throw error for null', () => {});
    it('should throw error for invalid date string', () => {});
  });
});
```

### Test Edge Cases
Always test:
- Null and undefined inputs
- Empty strings, arrays, objects
- Boundary conditions (min/max values)
- Error conditions

```typescript
describe('divideNumbers', () => {
  it('should divide two positive numbers', () => {
    expect(divideNumbers(10, 2)).toBe(5);
  });
  
  it('should handle negative numbers', () => {
    expect(divideNumbers(-10, 2)).toBe(-5);
  });
  
  it('should throw error when dividing by zero', () => {
    expect(() => divideNumbers(10, 0)).toThrow('Cannot divide by zero');
  });
});
```

## Running Tests

Available test commands:

```bash
# Run the test suite (default: watch mode in interactive terminals)
npm test

# Run tests in watch mode (auto-rerun on changes)
npm test -- --watch

# Run tests with UI
npm run test:ui

# Run tests with coverage
npm run test:coverage

# Run specific test file
npm test -- helpers.test.ts

# Run tests matching a pattern
npm test -- --grep "formatDate"
```

## Coverage Goals

- Aim for high coverage on utility functions (80%+)
- Focus on testing behavior, not implementation
- Don't write tests just to increase coverage
- Every exported function should have tests

## Testing Utilities

Example of a testable utility function:

```typescript
// src/utils/date-formatter.ts

/**
 * Format a date as YYYY-MM-DD
 */
export function formatDate(date: Date): string {
  const year = date.getFullYear();
  const month = String(date.getMonth() + 1).padStart(2, '0');
  const day = String(date.getDate()).padStart(2, '0');
  return `${year}-${month}-${day}`;
}
```

```typescript
// src/utils/date-formatter.test.ts

import { describe, it, expect } from 'vitest';
import { formatDate } from './date-formatter';

describe('formatDate', () => {
  it('should format date as YYYY-MM-DD', () => {
    const date = new Date(2024, 0, 15); // January 15, 2024
    const result = formatDate(date);
    expect(result).toBe('2024-01-15');
  });
  
  it('should pad single-digit months with zero', () => {
    const date = new Date(2024, 4, 5); // May 5, 2024
    const result = formatDate(date);
    expect(result).toBe('2024-05-05');
  });
  
  it('should handle December correctly', () => {
    const date = new Date(2024, 11, 31); // December 31, 2024
    const result = formatDate(date);
    expect(result).toBe('2024-12-31');
  });
});
```

## Best Practices

1. **Write tests as you code** - Don't wait until the end
2. **Test behavior, not implementation** - Tests should survive refactoring
3. **Keep tests simple** - Tests should be easier to understand than the code
4. **Use meaningful test data** - Avoid magic numbers and obscure values
5. **Don't test framework code** - Focus on your logic
6. **Isolate tests** - Each test should be independent
7. **Use descriptive variable names** - Make test intent clear

## When to Skip Tests

You may skip tests for:
- Astro component files (`.astro`) - these are primarily markup
- Simple configuration files
- Type definitions without logic
- Build scripts

Always test:
- Utility functions
- Helper functions
- Data transformations
- Business logic
- Functions with complex conditions

## Troubleshooting

### Tests not running
```bash
# Check if vitest is installed
npm list vitest

# Reinstall if needed
npm install
```

### Import errors in tests
- Check that imports use relative paths: `import { fn } from './module'`
- Verify the module exports the function
- Check TypeScript configuration

### Coverage not working
```bash
# Install coverage package if missing
npm install --save-dev @vitest/coverage-v8

# Run with coverage
npm run test:coverage
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/niek-tg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
