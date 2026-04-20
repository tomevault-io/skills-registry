---
name: testing
description: 测试规范与最佳实践。Use when writing unit tests, integration tests, or component tests. Triggers on: creating test files, using Vitest, Testing Library, mocking, test organization questions. Use when this capability is needed.
metadata:
  author: sammysnake-d
---

# Testing Guide

测试规范，涵盖 Vitest、Testing Library 和测试组织。

## Quick Start

```typescript
import { describe, it, expect } from 'vitest';

describe('ModuleName', () => {
  it('should behavior description', () => {
    expect(actual).toBe(expected);
  });
});
```

## Test Location

All tests in `__tests__/` directory, mirroring source structure:

```
__tests__/
├── lib/utils/
├── lib/services/
├── components/
└── hooks/
```

## File Naming

- Unit tests: `*.test.ts` / `*.test.tsx`
- Integration tests: `*.spec.ts` / `*.spec.tsx`

## Commands

```bash
pnpm test           # Run all tests
pnpm test:watch     # Watch mode
pnpm test:coverage  # Coverage report
```

## Component Test

```typescript
import { render, screen } from '@testing-library/react';
import { Button } from '@/components/ui/button';

describe('Button', () => {
  it('should render children', () => {
    render(<Button>Click</Button>);
    expect(screen.getByText('Click')).toBeInTheDocument();
  });
});
```

## Mocking

```typescript
import { vi } from 'vitest';

vi.mock('@/lib/services', () => ({
  UserService: {
    getUser: vi.fn().mockResolvedValue({ id: '1' }),
  },
}));
```

## Coverage Requirements

1. Normal path - Expected behavior
2. Edge cases - Empty, null, boundary values
3. Error handling - Exception scenarios

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sammysnake-d) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
