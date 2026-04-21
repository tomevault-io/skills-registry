---
name: module-depth
description: Reviews modules for depth vs shallowness. Use when designing APIs, reviewing class interfaces, or evaluating abstraction value. Use when this capability is needed.
metadata:
  author: bchapuis
---

# Module Depth Reviewer

Deep modules provide powerful functionality behind simple interfaces.

## Deep vs Shallow

- **Deep (good)**: Few methods, minimal parameters → hides significant complexity
- **Shallow (bad)**: Many methods, many parameters → little actual functionality

Examples of deep: Unix file I/O (5 calls hide tremendous complexity), TCP sockets, garbage collectors.

## Signs of Shallow Modules

- Pass-through methods (same signature delegation)
- Decorators adding no real value
- Over-decomposition into tiny classes
- Parameter explosion

## Evaluation Criteria

**Interface**: Public method count, parameters per method, concepts callers must understand
**Implementation**: What complexity is hidden? Real work or mostly delegation?
**Information Hiding**: Implementation details visible? Can it change without affecting callers?

## Output

Per module: interface complexity, functionality depth, assessment. Recommendations: combine shallow modules, simplify interfaces, hide more complexity.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bchapuis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
