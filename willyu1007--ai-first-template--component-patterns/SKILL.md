---
name: component-patterns
description: Component structure, prop patterns, and composition guidelines for React. Keywords: components, props, composition, react patterns, hooks. Use when this capability is needed.
metadata:
  author: willyu1007
---

# Component Patterns

This skill provides pragmatic patterns for React components.

---

## 1. Recommended component structure

Order (top to bottom):
1. Types (`Props`)
2. Hooks/state
3. Derived values (`useMemo` where useful)
4. Handlers (`useCallback` when passed to children)
5. Render

---

## 2. Props and boundaries

- Prefer small `Props` and explicit types.
- Avoid passing "bags of data"; pass exactly what the component needs.
- Keep components pure: do not fetch data directly inside presentational components unless that is the design.

---

## 3. Composition

- Prefer composition over deep prop drilling:
  - `children` slots
  - context providers (sparingly, with clear boundaries)
- Keep reusable components in a shared `components/` directory; keep domain-specific UI in `features/<feature>/`.

---

## 4. Common anti-patterns

- Inline anonymous functions in deep lists without need
- Multiple early returns that change layout unpredictably
- Doing expensive computation during render without memoization

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
