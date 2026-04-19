---
name: refactoring
description: Safe code refactoring with step-by-step approach Use when this capability is needed.
metadata:
  author: duhenri9
---

# Refactoring Guide

## When to Use
Use this skill when refactoring code or improving code structure.

## Refactoring Principles

### 1. Safety First
- Ensure tests exist before refactoring
- Make small, incremental changes
- Commit after each successful step
- Keep functionality identical

### 2. Common Refactorings

#### Extract Method
When: Code block does one thing and can be named
```
Before: Long method with multiple responsibilities
After: Small methods with clear names
```

#### Extract Variable
When: Complex expression needs explanation
```
Before: if (user.age >= 18 && user.country === 'US' && user.verified)
After: const canPurchase = user.age >= 18 && user.country === 'US' && user.verified;
```

#### Replace Conditional with Polymorphism
When: Switch/if statements based on type
```
Before: switch(type) { case 'A': ... case 'B': ... }
After: type.process() // Each type implements process()
```

### 3. Step-by-Step Process
1. Identify code smell
2. Write characterization tests if missing
3. Apply refactoring
4. Run tests
5. Commit
6. Repeat

## Code Smells to Watch
- Long methods (>20 lines)
- Long parameter lists (>3 params)
- Duplicate code
- Feature envy
- Data clumps
- Primitive obsession

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duhenri9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
