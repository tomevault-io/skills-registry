---
name: refactoring-guide
description: Strategies for cleaning up code without breaking it. Use when code becomes messy or hard to read. Use when this capability is needed.
metadata:
  author: wynautbhav
---

# Refactoring Guide

## Techniques
1. **Extract Function**: If a function is >20 lines, break it into smaller helper functions.
2. **Rename**: If you have to read the code to know what a variable does, rename the variable.
3. **Guard Clauses**: Replace nested `if/else` with early returns.
   *Bad*: `if (A) { if (B) { do() } }`
   *Good*: `if (!A) return; if (!B) return; do();`
4. **Remove Dead Code**: If it's commented out, delete it. Git remembers history.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wynautbhav) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
