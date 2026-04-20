---
name: testing-strategy
description: Comprehensive testing with Vitest, Cypress, and React Testing Library targeting 80%+ coverage with Three.js component testing Use when this capability is needed.
metadata:
  author: hack23
---

# Testing Strategy Skill

## Context
Applies when writing unit tests, E2E tests, testing Three.js components, mocking dependencies, measuring coverage, or debugging test failures.

## Rules

1. **80%+ Coverage**: Minimum across lines, branches, functions, statements
2. **Test Pyramid**: Most unit tests, fewer integration, fewest E2E
3. **Behavior Not Implementation**: Test what code does, not how
4. **AAA Pattern**: Arrange (setup), Act (execute), Assert (verify)
5. **Mock External Dependencies**: APIs, timers, Three.js renderer — with proper TypeScript types
6. **Test Error Cases**: Both success and failure paths
7. **Test Edge Cases**: Boundary conditions, empty inputs, null/undefined
8. **React Testing Library**: Prefer role/text queries; when components expose stable test IDs, using `data-testid` selectors is acceptable
9. **Test Logic Not Rendering**: For Three.js, test state/logic, mock renderer
10. **Isolate Tests**: Independent, no shared state, `vi.clearAllMocks()` in beforeEach
11. **Descriptive Names**: "should [behavior] when [condition]"
12. **Deterministic**: Mock `Date.now()`, `Math.random()`, timers. No real network calls
13. **Keep Tests Fast**: Unit in ms, E2E in seconds
14. **CI Must Pass**: All tests must pass before merge

## Examples

### ✅ Component Test with RTL

```tsx
import { render, screen } from '@testing-library/react';
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { HUD } from './HUD';

type GameState = {
  score: number;
  isPlaying: boolean;
  timeLeft: number;
  combo: number;
  highScore: number;
  targetSize: number;
  level: number;
  isNewHighScore: boolean;
  targets: unknown[];
  totalClicks: number;
  successfulHits: number;
};

const mockGameState: GameState = {
  score: 42, isPlaying: true, timeLeft: 30, combo: 0,
  highScore: 100, targetSize: 0.5, level: 1, isNewHighScore: false,
  targets: [], totalClicks: 10, successfulHits: 5,
};

describe('HUD', () => {
  beforeEach(() => { vi.clearAllMocks(); });

  it('should display current score', () => {
    render(<HUD gameState={mockGameState} />);
    expect(screen.getByText(/42/)).toBeInTheDocument();
  });
});
```

### ✅ Game Logic Test (Pure Function)

```typescript
import { describe, it, expect } from 'vitest';
import { calculateLevel, getTargetCountForLevel } from './gameConfig';

describe('gameConfig', () => {
  it('should return level 1 for score 0', () => {
    expect(calculateLevel(0)).toBe(1);
  });

  it('should return 1 target for levels 1-3', () => {
    expect(getTargetCountForLevel(1)).toBe(1);
    expect(getTargetCountForLevel(3)).toBe(1);
  });
});
```

### ✅ Three.js Mock

```tsx
import type { ReactNode } from 'react';
import { vi } from 'vitest';

vi.mock('@react-three/fiber', () => ({
  Canvas: ({ children }: { children: ReactNode }) => <div>{children}</div>,
  useFrame: vi.fn(),
  useThree: () => ({ camera: {}, scene: {}, gl: {} }),
}));

vi.mock('@react-three/drei', () => ({
  Sparkles: ({ children }: { children?: ReactNode }) => <>{children}</>,
  Trail: ({ children }: { children?: ReactNode }) => <>{children}</>,
}));
```

### ❌ Anti-Patterns

```typescript
// BAD: Testing implementation
expect(component.state.internalValue).toBe(5);

// BAD: Non-deterministic
expect(result).toBe(Math.random());

// BAD: Vague name
it('works', () => { /* test omitted */ });

// BAD: Shared state between tests
let counter = 0;
it('test1', () => { counter++; });
it('test2', () => { expect(counter).toBe(1); }); // Order dependent!
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hack23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
