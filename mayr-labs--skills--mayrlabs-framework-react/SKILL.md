---
name: mayrlabs-framework-react
description:
  React standard enforcing functional components, custom hooks, strict
  separation of state from UI, and memoization.
license: MIT
metadata:
  author: MayR Labs
  version: '1.0'
---

# MayR Labs React Doctrine

## Purpose

To maintain scalable and performant React applications by eliminating bloated
components, prop-drilling, and unstructured side-effects.

## Audience

AI Agents scaffolding structural React UI, creating components, or managing
frontend state.

## Core Rules & Constraints

### 1. Functional Components Only

- No class components. Always use functional components and hooks.
- Use explicit return typing (`React.FC` is discouraged, just type the
  parameters and return `JSX.Element` natively).

### 2. Isolate Business Logic

- Components (UI) must be "dumb". If a component is managing complex state
  transitions, fetching data directly, or applying business logic, extract it.
- **Custom Hooks**: Abstract heavy logic into `use[Feature]` custom hooks to
  keep the UI layer strictly declarative.

### 3. State & Effects

- Avoid setting state inside a `useEffect` if the state can be computed directly
  from props during render.
- If an object or array is a dependency in `useEffect`, ensure it is memoized
  (`useMemo`, `useCallback`) to prevent infinite re-render loops.

## Inputs & Outputs

- **Input**: Editing `.jsx` or `.tsx` React files.
- **Output**: Predictable, clean, and isolated functional React components.

---
> Source: [MayR-Labs/skills](https://github.com/MayR-Labs/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->
