---
name: tdd
description: Enforce TDD with Red-Green-Refactor cycle Use when this capability is needed.
metadata:
  author: thm-design
---

# TDD Workflow

## RED 🔴 - Write Failing Test
```typescript
import { describe, it, expect } from 'vitest'
import { myFunction } from '../myFunction'

describe('myFunction', () => {
  it('should return expected result', () => {
    expect(myFunction('input')).toBe('expected')
  })
})
```

Run: `pnpm test` → Must see FAIL

## GREEN 🟢 - Minimal Implementation
Write ONLY what makes the test pass.
No extras. No "nice to haves."

Run: `pnpm test` → Must see PASS

## REFACTOR 🔵 - Clean Up
Only after green. Run tests again.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thm-design) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
