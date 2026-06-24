---
name: write-tests
description: Write unit tests, integration tests, or E2E tests for code. Use after implementing a feature or when test coverage is needed. Use when this capability is needed.
metadata:
  author: yu-iskw
---

# Write Tests

Write tests for the following:

$ARGUMENTS

## Testing Strategy

1. **Identify what to test**: Read the source code to understand behavior
2. **Choose test type**:
   - **Unit tests**: Individual functions/classes
   - **Integration tests**: Component interactions
   - **E2E tests**: Full user flows
3. **Write tests** following project patterns

### Test Template

```text
describe("ComponentName", () => {
  it("should handle normal input", () => {
    // Arrange → Act → Assert
  });

  it("should handle edge cases", () => {
    // Empty, null, boundary values
  });

  it("should handle errors", () => {
    // Error paths and exceptions
  });
});
```

1. **Run tests** to verify they pass

## Test Coverage Goals

- Utility functions: 90%+
- Core logic: 80%+
- API endpoints: 80%+
- UI components: 70%+

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yu-iskw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
