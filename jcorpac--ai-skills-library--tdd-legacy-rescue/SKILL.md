---
name: tdd-legacy-rescue
description: Strategies for bringing legacy code under test and refactoring it safely. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Legacy Code Rescue

"Legacy code is code without tests." — Michael Feathers

## Rescue Strategies

### 1. Sprout Method / Class
- When you need to add functionality, write it as new, tested code (a "sprout") and call it from the legacy code.
- **Benefit**: Ensures new code is tested immediately.

### 2. Wrap Method / Class
- Wrap an existing method or class with a new one that contains the new logic and calls the original.
- **Benefit**: Provides a clear boundary for testing new behavior.

### 3. Breaking Dependencies
- Identify "Seams" (places where you can change behavior without editing code in that place).
- Use Dependency Injection or Subclass & Override to isolate the logic you want to test.

## The Rescue Cycle
1. Identify a target for change.
2. Find a Seam and break dependencies.
3. Write a characterization test (to document current behavior).
4. Apply your change using TDD.
5. Refactor with the safety of your new tests.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
