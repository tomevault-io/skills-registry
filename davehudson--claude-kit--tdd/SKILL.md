---
name: tdd
description: Test-Driven Development workflow and patterns. Auto-loads when writing tests, creating components with tests, or following TDD methodology. Use when this capability is needed.
metadata:
  author: davehudson
---

TDD workflow for React/TypeScript development using Vitest and Testing Library.

For testing patterns, see `testing-patterns.md`. For Storybook patterns, see `storybook-patterns.md`.

---

## RED-GREEN-REFACTOR Workflow

**ALWAYS follow this order:**

### Phase 1: RED (Write Failing Test)

```typescript
import { describe, it, expect, vi } from "vitest";
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { ComponentName } from "./ComponentName";

describe("ComponentName", () => {
  it("renders without crashing", () => {
    render(<ComponentName />);
    expect(screen.getByRole("...")).toBeInTheDocument();
  });

  it("handles user interactions", async () => {
    const user = userEvent.setup();
    const handleClick = vi.fn();
    render(<ComponentName onClick={handleClick} />);
    await user.click(screen.getByRole("button"));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

Run test. **Expected: Tests FAIL** (component doesn't exist)

### Phase 2: GREEN (Minimal Implementation)

```typescript
"use client"; // Only if client component

interface ComponentNameProps {
  onClick?: () => void;
}

export function ComponentName({ onClick }: ComponentNameProps) {
  return (
    <div>
      <button onClick={onClick}>Action</button>
    </div>
  );
}
```

Run test again. **Expected: All tests PASS.**

### Phase 3: REFACTOR

Improve code while keeping tests green:
- Extract reusable patterns
- Improve naming and readability
- Ensure 80%+ test coverage

---

## Component Scaffolding

For every component, create three files:

```
ComponentName/
├── ComponentName.tsx           # Implementation
├── ComponentName.test.tsx      # Tests (write first!)
└── ComponentName.stories.tsx   # Storybook story
```

---

## Coverage Requirements

**Target: 80%+ on all metrics**

```bash
bun run test:coverage
```

---

## TDD Mindset

- **Test behavior, not implementation** - What it does, not how
- **One assertion per test** - Clear failure messages
- **Descriptive test names** - Documents expected behavior
- **Isolate tests** - No shared state between tests
- **Fast feedback** - Run tests in watch mode

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davehudson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
