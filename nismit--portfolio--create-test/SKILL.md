---
name: create-test
description: Create tests using Vitest and React Testing Library Use when this capability is needed.
metadata:
  author: nismit
---

# Create Test: $ARGUMENTS

## Test File Location

- Component tests: `src/components/[Name]/[Name].test.tsx`
- Utility tests: `src/lib/[name].test.ts`
- Page tests: `src/app/[path]/page.test.tsx`

## Component Test Template

```tsx
import { render, screen } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import { ComponentName } from './index';

describe('ComponentName', () => {
  it('renders correctly', () => {
    render(<ComponentName />);
    expect(screen.getByRole('...')).toBeInTheDocument();
  });

  it('handles user interaction', async () => {
    const user = userEvent.setup();
    render(<ComponentName />);

    await user.click(screen.getByRole('button'));
    expect(...).toBe(...);
  });
});
```

## Utility Test Template

```ts
import { describe, it, expect } from 'vitest';
import { utilityFunction } from './utility';

describe('utilityFunction', () => {
  it('returns expected value', () => {
    const result = utilityFunction(input);
    expect(result).toBe(expected);
  });

  it('handles edge cases', () => {
    expect(utilityFunction(null)).toBe(...);
    expect(utilityFunction(undefined)).toBe(...);
  });
});
```

## Guidelines

- Test behavior, not implementation
- Use meaningful test descriptions
- Avoid mocking unless necessary
- Run `npm run test` to verify tests pass

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nismit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
