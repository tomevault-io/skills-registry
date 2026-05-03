---
name: react-readonly-query-kakao-perf
description: Use this skill when optimizing React frontend performance in apps that read data with TanStack React Query useQuery, do not perform mutations, and render third-party maps like Kakao Map after mount with useEffect or refs. It is especially relevant when some shared map or filter state is already kept in a store, but it does not recommend moving component-local UI state out of useState unless cross-component sharing is required. Apply it for render-cost analysis, query-key stability, effect minimization, marker synchronization, and preventing unnecessary rerenders around map/list UIs.
metadata:
  author: ryu9663
---

# React Readonly Query Kakao Perf

## Overview

Use this skill to analyze and improve runtime performance in React applications that:

- fetch read-only data with `useQuery`
- render a third-party map after mount
- synchronize markers or overlays from fetched list data

This skill assumes `useState` is the default for component-local UI state. Treat stores as a coordination tool for genuinely shared state, not as a default optimization technique.

Prioritize changes that reduce avoidable rerenders and repeated imperative work. Do not add complexity unless a measurable hot path exists.

## Workflow

1. Identify the render boundary.
   Inspect which components rerender when query params, map position, or loading state change.

2. Separate declarative state from imperative map work.
   Keep React responsible for query params, loading UI, and list rendering. Keep Kakao Map instance creation, marker updates, and drag listeners isolated behind refs and effects.

3. Stabilize query inputs.
   Ensure query keys are built from primitive values, and avoid recreating objects that only exist to call `queryFn`.

4. Reduce effect churn.
   Re-run effects only when the external system actually needs to change. A rerender is cheaper than tearing down and rebuilding third-party map artifacts.

5. Verify with a narrow measurement pass.
   Use React DevTools Profiler, temporary render counters, or targeted logs before and after the change. Avoid speculative memoization.

## What To Check First

### State Ownership

- Default to `useState` for component-local UI state.
- Use a store only when state must be shared across multiple distant components, coordinated with an external imperative system like a map, or consumed outside a single local subtree.
- Do not move local UI state into a store unless that sharing requirement is real.

### Query Layer

- Build query keys from primitives, not unstable object identities.
- Derive request params from current state once per render.
- If some params come from a store, read only the fields this query actually needs.
- Use `select` to trim server payload to the smallest shape the UI needs.
- For read-only flows, prefer `staleTime` and `gcTime` tuning over manual refetch patterns.
- If the same query is revisited frequently from map drags or filter toggles, consider increasing `staleTime` to reduce repeat fetches.

### Component Boundaries

- Split map container, map overlay controls, and list items so a query-state change does not always force all of them to rerender.
- Avoid passing freshly created arrays, objects, and callbacks into large subtrees unless those values are semantically new.
- Memoize only around proven hot paths such as large place lists or expensive item formatting.

### Kakao Map Integration

- Create the map instance once per container lifecycle unless center/level recreation is explicitly required.
- Store the map instance and marker instances in refs, not state, when React does not need to render from them.
- Update markers incrementally when possible; avoid full remove-and-recreate cycles on unrelated state changes.
- Register map drag or zoom listeners once, and clean them up on unmount.
- Never put unstable third-party constructor references in an effect dependency list just to satisfy lint.

## Preferred Patterns

### 1. Read-only React Query

- Keep `queryKey` primitive and explicit.
- Keep `queryFn` focused on network I/O.
- Use `select` for shape reduction.
- Tune caching before adding client-side mirrors.

Example shape:

```tsx
const query = useQuery({
  queryKey: ['places', mapX, mapY, radius, numOfRows],
  queryFn: () => getPlaces({ mapX, mapY, radius, numOfRows }),
  select: data => data.response.body.items.item,
  enabled: Boolean(position),
  staleTime: 30_000
});
```

### 2. Third-party Map Lifecycle

- `useRef` for map instance
- one effect for mount-time initialization
- one effect for marker synchronization
- one effect for event subscription if subscriptions depend on the map instance

Avoid patterns where marker arrays are kept in React state unless the UI renders from that state.

### 3. Choosing Store Boundaries

- Select only the store fields required by the component.
- Avoid binding map-only state to list-only components.
- If a store update is high frequency, check whether every subscriber truly needs it.

## Smells

- `useEffect` depends on values that do not semantically require a third-party redraw.
- Marker instances are stored in React state even though they never affect JSX output.
- Map initialization runs again because options objects or constructor references are recreated.
- Query params are assembled into a new object in multiple layers, obscuring what actually changes.
- Large lists rerender because parent components own unrelated loading or map state.
- Lint suppression hides a dependency problem instead of making the effect boundaries correct.

## Decision Rules

### When To Use `useRef` Instead Of `useState`

Use `useRef` when the value:

- is imperative
- does not affect rendered JSX
- should survive rerenders without causing a rerender

Typical examples in this stack:

- Kakao map instance
- marker arrays
- overlay handles
- unsubscribe or listener handles

### When To Memoize

Add `useMemo` or `React.memo` only if at least one of these is true:

- profiling shows repeated expensive derivation
- a child subtree is large and rerenders with identical inputs
- referential stability is required by a third-party API boundary

Do not memoize primitive query params or trivial object creation unless it unblocks a proven rerender issue.

## Repo-Specific Guidance For This Stack

For projects shaped like `Home -> HomeWithPosition -> KakaoMap + PlaceList`:

- Keep geolocation suspense and skeleton handling at the page boundary.
- Keep `usePlacesQuery` responsible for network params and payload selection only.
- Keep `KakaoMap` focused on container mounting and shared map coordination.
- Push marker bookkeeping into the map hook, but prefer refs over state for previous markers.
- If drag updates store `pickPoint`, confirm that only components that need `pickPoint` subscribe to it.
- If query keys already use primitives, optimize rerender boundaries before changing the query layer.

## Output Expectations

When using this skill, produce:

1. a short diagnosis of the dominant bottleneck
2. the smallest safe code change that addresses it
3. a verification step

If no meaningful bottleneck is demonstrated, say so and avoid decorative optimization.

---
> Source: [ryu9663/aneun-dongne-client](https://github.com/ryu9663/aneun-dongne-client) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->
