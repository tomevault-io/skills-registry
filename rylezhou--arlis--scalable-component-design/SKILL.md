---
name: designing-scalable-components-hooks
description: Guidelines for creating reusable, resilient, and clean components, hooks, and utilities in a React/Next.js application. Use when this capability is needed.
metadata:
  author: rylezhou
---

# Designing Scalable Components & Hooks

This skill defines the architectural standards for building software units (Components, Hooks, Utils) that are easy to test, maintain, and scale.

## 1. The Hook-First Architecture
Before writing complex logic inside a React Component, ask: **"Can this be a custom hook?"**

### Why?
- **Separation of Concerns**: Logic lives in `.ts` files, UI lives in `.tsx` files.
- **Reusability**: The same logic can power multiple UIs (e.g., `useChatMessages` powering a Sidebar and a Main View).
- **Testability**: Hooks are easier to test than full components.

### Pattern: The "Data-Logic-UI" Split
1. **The Query Hook** (`queries.ts`): Wraps the raw data fetch.
   - Responsible for: Caching keys, Fetch API calls, Typing.
2. **The Logic Hook** (`useChatMessages.ts`): Consumes the query, transforms data.
   - Responsible for: Filtering, Sorting, Formatting, Derived State, Polling intervals.
3. **The Component** (`ChatInterface.tsx`): Consumes the Logic Hook.
   - Responsible for: Rendering, User interaction, Animation.

**Example:**
Instead of `usEffect` inside a component to sort and filter:
```tsx
// Bad: Logic mixed with UI
function Chat() {
  const { data } = useQuery(...);
  const messages = useMemo(() => data.filter(...).sort(...), [data]);
  return <List items={messages} />
}
```
**Do this:**
```tsx
// Good: Logic extracted
function Chat() {
  const messages = useChatMessages(id); // Returns ready-to-use data
  return <List items={messages} />
}
```

## 2. Component Design Principles

### A. Props Interface: Explicit vs. Derived
Prefer passing **Primitive IDs** over **Complex Objects** for "Smart" components.
- **Good**: `<UserProfile userId={props.id} />`
  - The component fetches what it needs. Resilient to parent data shape changes.
- **Okay**: `<UserProfile user={userObj} />`
  - Good for pure presentation components, but creates dependency on parent fetching *exactly* the right fields.

### B. "Server-Driven" Components
In a modern Next.js/React Query app, components should be autonomous.
- Avoid passing callback props (`onRefresh`, `setMessages`) if the component can just invalidate a query key or use a shared hook.
- **Optimistic Updates**: Handle them inside the mutation hook/logic, not by manually updating UI state props code.

### C. Formatting & Utilities
Never hardcode formatting logic in JSX.
- **Bad**: `<span>{date.toISOString().split('T')[0]}</span>`
- **Good**: `<span>{formatDate(date)}</span>`
- **Better**: Create a reusable utility file `lib/utils/formatters.ts`.

## 3. Directory Structure for Scale
When a feature grows, don't keep everything in one folder.
```
components/
  feature-name/
    index.tsx (export public API)
    feature-container.tsx (Main logic)
    feature-list.tsx (Sub-component)
    feature-item.tsx (Sub-component)
    use-feature-logic.ts (Local hook)
```

## 4. The "No-Op" Prop Pattern
When designing components that *might* be controlled or uncontrolled:
- Allow props to override internal state.
- Provide reasonable defaults so the component works "out of the box" without complex setup.

## 5. Resilience
- **Loading States**: Always handle `isLoading`.
- **Empty States**: Always handle `data.length === 0`.
- **Error States**: Handle API failures gracefully (Toast + UI feedback).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rylezhou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
