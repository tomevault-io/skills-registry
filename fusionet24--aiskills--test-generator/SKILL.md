---
name: test-generator
description: Activates when generating unit tests, integration tests, or test cases for code Use when this capability is needed.
metadata:
  author: fusionet24
---

# Test Generator

## Overview
This skill helps generate comprehensive test cases for various types of code. It creates unit tests, integration tests, and edge case tests that follow best practices and testing frameworks.

## Instructions
When generating tests, follow these steps:

1. **Analyze the Code**: Understand what the code does, its inputs, outputs, and dependencies
2. **Identify Test Cases**: Determine happy path, edge cases, and error scenarios
3. **Choose Framework**: Use the appropriate testing framework for the language
4. **Write Tests**: Create clear, maintainable test cases with descriptive names
5. **Add Assertions**: Include proper assertions and expected outcomes
6. **Document**: Add comments for complex test scenarios

## Examples

### Example 1: Python Function Test
**Input:**
```python
def calculate_discount(price, discount_percent):
    if discount_percent < 0 or discount_percent > 100:
        raise ValueError("Discount must be between 0 and 100")
    return price * (1 - discount_percent / 100)
```

**Output:**
```python
import pytest

def test_calculate_discount_valid():
    """Test discount calculation with valid inputs."""
    assert calculate_discount(100, 20) == 80.0
    assert calculate_discount(50, 10) == 45.0

def test_calculate_discount_zero():
    """Test with zero discount."""
    assert calculate_discount(100, 0) == 100.0

def test_calculate_discount_full():
    """Test with 100% discount."""
    assert calculate_discount(100, 100) == 0.0

def test_calculate_discount_negative():
    """Test that negative discount raises ValueError."""
    with pytest.raises(ValueError, match="Discount must be between 0 and 100"):
        calculate_discount(100, -10)

def test_calculate_discount_over_hundred():
    """Test that discount over 100 raises ValueError."""
    with pytest.raises(ValueError, match="Discount must be between 0 and 100"):
        calculate_discount(100, 150)
```

### Example 2: JavaScript API Test
**Input:**
```javascript
async function fetchUserData(userId) {
    const response = await fetch(`/api/users/${userId}`);
    if (!response.ok) throw new Error('Failed to fetch user');
    return response.json();
}
```

**Output:**
```javascript
import { describe, it, expect, vi } from 'vitest';

describe('fetchUserData', () => {
    it('should fetch user data successfully', async () => {
        global.fetch = vi.fn(() =>
            Promise.resolve({
                ok: true,
                json: () => Promise.resolve({ id: 1, name: 'John' })
            })
        );

        const data = await fetchUserData(1);
        expect(data).toEqual({ id: 1, name: 'John' });
        expect(fetch).toHaveBeenCalledWith('/api/users/1');
    });

    it('should throw error when fetch fails', async () => {
        global.fetch = vi.fn(() =>
            Promise.resolve({ ok: false })
        );

        await expect(fetchUserData(1)).rejects.toThrow('Failed to fetch user');
    });
});
```

## Notes
- Cover edge cases and error scenarios
- Use descriptive test names that explain what is being tested
- Keep tests isolated and independent
- Mock external dependencies
- Test both success and failure paths
- Follow the AAA pattern: Arrange, Act, Assert
- Consider performance and testing pyramid principles

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fusionet24) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
