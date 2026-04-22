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
   - **Unit tests** (Jest): Individual functions/classes — `*.test.ts` alongside source
   - **E2E tests** (Playwright): Full user flows — `packages/frontend/src/tests/`
   - **Evaluation** (LangSmith): AI agent quality — `eval/langsmith/` directories
3. **Write tests** following project patterns:

### Jest Test Template

```typescript
import { describe, it, expect, jest, beforeEach } from "@jest/globals";

describe("FunctionName", () => {
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

4. **Run tests** to verify they pass:
   - `pnpm test:agents` — Agent tests
   - `pnpm test:backend` — Backend tests
   - `pnpm test:common` — Common package tests
   - `pnpm test:frontend` — E2E tests

## Test Coverage Goals

- Utility functions: 90%+
- Agent nodes: 80%+
- API endpoints: 80%+
- Vue components: 70%+

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yu-iskw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
