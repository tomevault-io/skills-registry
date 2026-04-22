---
name: react-19-patterns
description: React 19 patterns for hooks, concurrency, and performance in Buzz Stack. Use when this capability is needed.
metadata:
  author: colten-covington
---

# React 19 Patterns

Use this skill for hook usage, memoization, and concurrent UI updates.

## Core Guidance

- Order hooks: state, callbacks, effects, render.
- Use `useTransition` for non-blocking updates.
- Use `useDeferredValue` to keep input responsive.
- Memoize expensive work with `useMemo` and stabilize callbacks with `useCallback`.

## References

- [Design patterns](../../../docs/DESIGN_PATTERNS.md)
- [Performance](../../../docs/PERFORMANCE.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/colten-covington) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
