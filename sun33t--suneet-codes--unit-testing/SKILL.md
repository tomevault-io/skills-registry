---
name: unit-testing
description: This skill should be used when the user asks to "add unit tests", "write tests", "set up Vitest", "create a utility function", "add test coverage", or mentions TDD, test-driven development, or testing utilities. Provides guidance for unit testing with Vitest in TypeScript/Next.js projects using TDD approach. Use when this capability is needed.
metadata:
  author: sun33t
---

# Unit Testing with Vitest

Scope: Unit tests only. See separate skills for integration/E2E.

## TDD Workflow

```
RED → GREEN → REFACTOR
```

1. **RED**: Write failing test first (function doesn't exist yet)
2. **GREEN**: Write minimal code to pass
3. **REFACTOR**: Clean up, run tests after each change

## Setup

```bash
pnpm add -D vitest
```

Create `vitest.config.mts`:

```typescript
import { defineConfig } from "vitest/config";

export default defineConfig({
  test: {
    globals: true,
    environment: "node",
    include: ["**/*.test.ts"],
    exclude: ["node_modules", ".next", ".content-collections", "e2e"],
  },
});
```

Add to `tsconfig.json`:

```json
{ "compilerOptions": { "types": ["vitest/globals"] } }
```

Add scripts:

```json
{ "test": "vitest", "test:run": "vitest run" }
```

For complete setup options, see [references/vitest-setup.md](references/vitest-setup.md).

## Test Structure

Place tests alongside code: `myFunction.ts` → `myFunction.test.ts`

```typescript
import { describe, expect, it } from "vitest";
import { myFunction } from "./myFunction";

describe("myFunction", () => {
  describe("valid inputs", () => {
    it("handles primary case", () => {
      expect(myFunction("input")).toBe("expected");
    });
  });

  describe("invalid inputs", () => {
    it("throws for bad input", () => {
      expect(() => myFunction("bad")).toThrow();
    });
  });
});
```

## Key Patterns

**Zod validation** - use `safeParse`:

```typescript
it("rejects invalid", () => {
  const result = Schema.safeParse(badData);
  expect(result.success).toBe(false);
});

it("checks error message", () => {
  const result = Schema.safeParse(badData);
  if (!result.success) {
    expect(result.error.errors[0].message).toContain("expected");
  }
});
```

For mocking, async, parameterized tests, see [references/test-patterns.md](references/test-patterns.md).

## Run Tests

```bash
pnpm test:run                              # all tests
pnpm test:run lib/utils/myFunction.test.ts # specific file
pnpm test:run -t "pattern"                 # matching pattern
```

## Checklist

- [ ] Tests cover primary use case
- [ ] Tests cover edge cases (empty, boundary)
- [ ] Tests cover error cases
- [ ] Descriptive test names
- [ ] Independent tests (no shared state)
- [ ] Mocks cleared (`vi.clearAllMocks()` in `beforeEach`)
- [ ] All tests pass

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sun33t) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
