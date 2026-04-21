---
name: refactoring-code
description: Refactors ONE component or module. Use when user asks to refactor, clean up, restructure, or improve code organization. Use when this capability is needed.
metadata:
  author: gabbloquet
---

# Refactor Code

## Process
1. Read current implementation
2. Identify improvement area
3. Apply changes incrementally
4. Verify behavior unchanged

## Refactoring Types
| Type | Description |
|------|-------------|
| Extract | Move code to separate function/class |
| Rename | Improve naming clarity |
| Simplify | Reduce complexity |
| Reorganize | Better file structure |

## Rules
- Keep same external behavior
- One refactor at a time
- Run tests after each change
- Update imports if files move

## Extract Pattern
```typescript
// Before: inline logic
if (a > b && c < d && e === f) { ... }

// After: extracted function
function shouldProcess(a, b, c, d, e, f): boolean {
  return a > b && c < d && e === f;
}
```

## Example
Input: "Refactorise le calcul de dégâts"
Output: Extract damage calculation to separate function with clear parameters

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gabbloquet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
