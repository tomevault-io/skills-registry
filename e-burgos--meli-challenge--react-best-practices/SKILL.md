---
name: react-best-practices
description: Applies current React best practices for components, hooks, composition, performance, and accessibility. Use when writing or reviewing React code, components, hooks, or when working with React 19 and Server Components. Use when this capability is needed.
metadata:
  author: e-burgos
---

# React Best Practices

## Components: functional and pure

- **Functional components only**: Use function components; avoid class components. Hooks are only available in functions.
- **Purity**: Components must be pure: same props/state/context → same output. No side effects during render; no mutating non-local values. Put side effects in event handlers or `useEffect`.
- **Single responsibility**: One clear purpose per component; split when a component does too much. Prefer composition over large, multi-concern components.

## Rules of Hooks

- **Top level only**: Call hooks only at the top level of a component or custom hook—not inside loops, conditions, nested functions, or try/catch.
- **From React only**: Call hooks only from function components or custom hooks, not from plain JS functions.
- Use **eslint-plugin-react-hooks** to enforce these rules.

## State and data

- **Lift state** to the lowest common ancestor that needs it; avoid storing state higher than necessary.
- **Derived state**: Prefer computing derived values during render (or with `useMemo` only when the computation is expensive and dependencies are stable). Avoid storing derived state in state when it can be computed.
- **Immutability**: Do not mutate props or state; update with new objects/arrays (spread, map, filter, etc.) so React can detect changes.

## Composition

- **Children and slots**: Prefer passing React elements as `children` or named props (e.g. `header`, `footer`) instead of prop drilling. Intermediate components stay dumb and layout-focused.
- **Compound components**: When several subcomponents share internal state, group them (e.g. with context) but expose a declarative API so the consumer composes the pieces they need.
- **Containment**: Use composition (children, render props, or slots) instead of config objects that duplicate the DOM structure.

## Effects (useEffect)

- **Side effects only**: Use `useEffect` for sync with external systems, subscriptions, or DOM; not for transforming props into state (prefer derived values or reset in render).
- **Dependencies**: List all reactive values used inside the effect in the dependency array; avoid lying about dependencies to satisfy the linter.
- **Cleanup**: Return a cleanup function when the effect sets up subscriptions, timers, or listeners so they are removed on unmount or when dependencies change.
- **Avoid effects for derived data**: If the effect only updates state from props, consider computing during render or using a key to reset state when identity changes.

## Performance

- **Measure first**: Use React DevTools Profiler (or similar) before adding memoization; optimize only when there is a measurable problem.
- **useMemo**: Use when a calculation is expensive and dependencies are stable; avoid for cheap computations or when dependencies change every render.
- **useCallback**: Use when passing a callback to a **memoized** child (React.memo) and the callback identity would cause unnecessary re-renders; or when the callback is a dependency of useEffect/useMemo. Do not use by default for every handler.
- **React.memo**: Use for components that re-render often with the same props; skip for components that almost always get new props or are cheap to render.
- **Code splitting**: Use `React.lazy` and `Suspense` for route-level or heavy components to reduce initial bundle size.

## Lists and keys

- **Stable keys**: Use a unique, stable id (e.g. from data) for list items. Do not use array index as key when the list can reorder, filter, or insert items—it can cause bugs and wrong reuse.
- **Key placement**: Put the key on the outermost element returned in the list (e.g. on the component you map, not on a wrapper fragment without key).

## Accessibility

- **Semantic HTML**: Use correct elements (`button`, `nav`, `main`, `label`, etc.); avoid div/span for interactive or structural roles when a native element exists.
- **ARIA when needed**: Use ARIA attributes when the default semantics are not enough (e.g. `aria-label`, `aria-expanded`, `role`); prefer native semantics first.
- **Focus**: Ensure keyboard focus order and visible focus (e.g. `:focus-visible`); do not remove outline without a visible replacement. Trap focus in modals when appropriate.
- **Forms**: Associate labels with inputs (`htmlFor`/`id`); report validation errors accessibly.

## React 19 and Server Components (when applicable)

- **Server Components**: Use for static or server-fetched content; no hooks or browser APIs. Fetch data with async/await in the component when the framework supports it.
- **Client Components**: Add `"use client"` only where needed (hooks, event handlers, browser APIs). Keep the client boundary as low as possible to reduce bundle size.
- **Server Actions**: Mark server-side mutation functions with `'use server'` at the top of the file or function; validate and authorize all arguments. Prefer calling them from forms or inside `useTransition`.
- **Frameworks**: Use React 19 with a framework that supports RSC (e.g. Next.js 15+, Remix, React Router 7+) when using Server Components.

## TypeScript (when used)

- Type component props with an interface or type; avoid inline object types for complex props.
- Type hooks (useState, custom hooks) so that initial value and updates are correctly typed; use generics for useState when the value can be null initially.
- Type event handlers (e.g. `React.ChangeEvent<HTMLInputElement>`) instead of `any`.

## Checklist

- [ ] Components are functional and pure; side effects only in handlers or useEffect.
- [ ] Hooks called at top level only; eslint-plugin-react-hooks enabled.
- [ ] State lifted appropriately; no redundant derived state.
- [ ] Composition used (children, compound components) instead of prop drilling or giant components.
- [ ] useMemo/useCallback/React.memo only where profiling shows benefit.
- [ ] List keys are stable and not index when list can change.
- [ ] Semantic HTML and accessibility considered (focus, labels, ARIA if needed).

## Reference

- Rules of Hooks, purity, Server Components: [reference.md](reference.md)
- React docs: [react.dev](https://react.dev)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/e-burgos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
