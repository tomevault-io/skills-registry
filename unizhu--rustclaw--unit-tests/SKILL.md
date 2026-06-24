---
name: unit-testing
description: Creates and runs unit tests for individual functions and modules. Use when writing or running unit tests.
metadata:
  author: unizhu
---

# Unit Testing

Focuses on testing individual units of code in isolation.

## Instructions

When creating unit tests:

1. **Identify test cases**
   - Normal inputs and expected outputs
   - Edge cases (empty, null, boundary values)
   - Error conditions

2. **Write test structure**
   ```rust
   #[test]
   fn test_function_name_scenario() {
       // Arrange
       let input = ...;
       
       // Act
       let result = function(input);
       
       // Assert
       assert_eq!(result, expected);
   }
   ```

3. **Best practices**
   - One assertion per test when possible
   - Clear, descriptive test names
   - Test behavior, not implementation

## Examples

**Input:** Create unit tests for add function
**Output:** Tests covering positive numbers, negative numbers, zeros, and overflow cases.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unizhu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
