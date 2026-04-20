---
name: test-naming-standards
description: Enforces test file and test case naming conventions. Use when writing tests, creating test files, or when code review requires proper test naming. Use when this capability is needed.
metadata:
  author: davidkk
---

# Test Naming Standards

## Test File Naming

- Unit test files use `.spec.ts` suffix
- E2E test files use `.spec.ts` suffix
- Test file directory structure should mirror the source code directory structure

## Test Case Naming

Test case naming format: `should [expected behavior] [conditions/scenarios]`

- Use descriptive names that clearly indicate what is being tested
- Use lowercase for the first letter
- Avoid using "test" at the beginning of test names

**Correct Examples:**

```typescript
it('should merge JSON files correctly - basic case', () => {
  // ...
})

it('should handle edge case: a has b does not', () => {
  // ...
})
```

**Incorrect Examples:**

```typescript
// ❌ Incorrect: Unclear what is being tested
it('test merge', () => {
  // ...
})

// ❌ Incorrect: Not descriptive enough
it('merge', () => {
  // ...
})
```

## Best Practices

1. **Be specific**: Test names should clearly describe the expected behavior
2. **Include conditions**: When testing edge cases, include the condition in the name
3. **Use lowercase**: Start test names with lowercase letters
4. **Avoid "test" prefix**: The framework already knows it's a test

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidkk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
