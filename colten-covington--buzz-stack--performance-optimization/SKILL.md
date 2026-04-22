---
name: performance-optimization
description: Implementation-focused performance optimization patterns for Buzz Stack (React 19 + Next.js): rendering, bundles, caching, network, runtime, and images. Use when this capability is needed.
metadata:
  author: colten-covington
---

# Performance Optimization

## Overview

This skill is about **making things faster**, not just measuring them.

- **Profiling** answers: “what is slow and why?”
- **Optimization** answers: “what should we change to improve it?”

Buzz Stack already documents performance concepts (Core Web Vitals, caching, concurrency). This skill turns them into a **repeatable implementation playbook**.

## Core Concepts

### 1) React Rendering Optimization

Common goals:

- reduce unnecessary renders
- prevent expensive recomputation
- keep input responsive during heavy updates

Tools:

- `React.memo` (component memoization)
- `useMemo`, `useCallback` (memoize expensive values / stable callbacks)
- React 19 concurrency (`useTransition`, `useDeferredValue`)

---

### 2) Bundle Size Reduction

Bundle size affects:

- time to interactive
- mobile performance
- low-end device UX

Primary levers:

- dynamic imports
- tree shaking
- avoid “barrel exports” that pull too much
- split rarely-used features (modals, editors, dashboards)

---

### 3) Caching Strategies (Client, Edge, Server)

Caching is performance’s multiplier—if safe.

Layers:

- HTTP caching (CDN/edge)
- in-memory caching (service layer)
- browser caching (service workers / headers)

Key concept: invalidation.

---

### 4) Network Optimization

Network improvements are often the highest ROI:

- parallelize independent requests
- deduplicate requests
- prefetch likely navigations
- avoid sending oversized payloads

---

### 5) Runtime Performance (JS + Layout)

Common pitfalls:

- O(n²) loops on large arrays
- layout thrashing (measure → mutate → measure loops)
- heavy main-thread work (parsing, sorting) during typing

Mitigations:

- move heavy compute into memoized steps
- chunk work using `requestIdleCallback`
- offload to Web Workers if needed

---

### 6) Image Optimization

Next.js makes image optimization approachable:

- `next/image`
- responsive sizes
- modern formats
- lazy loading

---

## Patterns (10+)

### Pattern 1: Use React 19 Concurrency for Non-Urgent Updates

```tsx
"use client";

import { useTransition } from "react";

export function Search({ onCompute }: { onCompute: (q: string) => void }) {
  const [isPending, startTransition] = useTransition();

  return (
    <div>
      <input
        type="text"
        onChange={(e) => {
          const value = e.target.value;
          startTransition(() => onCompute(value));
        }}
      />
      {isPending ? <p>Updating…</p> : null}
    </div>
  );
}
```

When to use:

- search filtering
- sorting large lists
- rendering expensive views

---

### Pattern 2: Defer Expensive Derived Values

```tsx
"use client";

import { useDeferredValue, useMemo } from "react";

export function FilteredList({
  query,
  items,
}: {
  query: string;
  items: string[];
}) {
  const deferredQuery = useDeferredValue(query);

  const results = useMemo(() => {
    const q = deferredQuery.trim().toLowerCase();
    if (!q) return items;
    return items.filter((x) => x.toLowerCase().includes(q));
  }, [deferredQuery, items]);

  return (
    <ul>
      {results.map((x) => (
        <li key={x}>{x}</li>
      ))}
    </ul>
  );
}
```

---

### Pattern 3: `React.memo` for Stable, Pure Children

```tsx
import { memo } from "react";

interface RowProps {
  title: string;
  onSelect: () => void;
}

export const Row = memo(function Row({ title, onSelect }: RowProps) {
  return <button onClick={onSelect}>{title}</button>;
});
```

Rules:

- Only helps if props are stable (use `useCallback` upstream)
- Only helps if the component is relatively expensive

---

### Pattern 4: Stabilize Callbacks Passed Down

```tsx
"use client";

import { useCallback, useState } from "react";

export function Parent({ titles }: { titles: string[] }) {
  const [selected, setSelected] = useState<string | null>(null);

  const select = useCallback((t: string) => {
    setSelected(t);
  }, []);

  return (
    <div>
      {titles.map((t) => (
        <Row key={t} title={t} onSelect={() => select(t)} />
      ))}
      <p>Selected: {selected ?? "none"}</p>
    </div>
  );
}
```

Note: the inline `() => select(t)` still creates a function; if this is a problem, pre-bind via memoized map (advanced) or restructure props.

---

### Pattern 5: Virtualize Very Large Lists

If you render thousands of rows, consider list virtualization.

Minimal pattern (conceptual):

```typescript
interface VirtualWindow {
  startIndex: number;
  endIndex: number;
}

function computeWindow(input: {
  scrollTop: number;
  rowHeight: number;
  viewportHeight: number;
  totalRows: number;
  overscan: number;
}): VirtualWindow {
  const visible = Math.ceil(input.viewportHeight / input.rowHeight);
  const start = Math.max(
    0,
    Math.floor(input.scrollTop / input.rowHeight) - input.overscan,
  );
  const end = Math.min(
    input.totalRows - 1,
    start + visible + input.overscan * 2,
  );
  return { startIndex: start, endIndex: end };
}
```

---

### Pattern 6: Dynamic Imports for Rarely-Used UI

```tsx
import dynamic from "next/dynamic";

const HeavyModal = dynamic(() => import("@/components/HeavyModal"), {
  loading: () => <p>Loading…</p>,
});

export function Page() {
  return <HeavyModal />;
}
```

Use for:

- modals
- charts
- editors

---

### Pattern 7: Tree Shaking Friendly Exports

```typescript
// ✅ good: named exports, tree-shakeable
export function normalize(text: string): string {
  return text.toLowerCase().trim();
}

// ❌ avoid: default object export can reduce tree shaking
const api = { normalize };
export default api;
```

---

### Pattern 8: Request Deduplication (Service Layer)

If multiple components ask for the same resource, dedupe it.

```typescript
type CacheEntry<T> = { value: T; expiresAt: number };

type InFlight<T> = Promise<T>;

export class RequestCache<T> {
  private cache = new Map<string, CacheEntry<T>>();
  private inflight = new Map<string, InFlight<T>>();

  constructor(private readonly ttlMs: number) {}

  async get(key: string, fetcher: () => Promise<T>): Promise<T> {
    const now = Date.now();
    const hit = this.cache.get(key);
    if (hit && hit.expiresAt > now) return hit.value;

    const pending = this.inflight.get(key);
    if (pending) return pending;

    const promise = fetcher()
      .then((value) => {
        this.cache.set(key, { value, expiresAt: Date.now() + this.ttlMs });
        this.inflight.delete(key);
        return value;
      })
      .catch((err) => {
        this.inflight.delete(key);
        throw err;
      });

    this.inflight.set(key, promise);
    return promise;
  }
}
```

---

### Pattern 9: HTTP Caching with `stale-while-revalidate`

For GET endpoints, set cache headers intentionally.

```typescript
import { NextResponse } from "next/server";

export function withCache(response: NextResponse): NextResponse {
  response.headers.set(
    "Cache-Control",
    "public, max-age=60, stale-while-revalidate=300",
  );
  return response;
}
```

---

### Pattern 10: Parallelize Independent Requests

```typescript
async function loadDashboard() {
  const [profile, stats, notifications] = await Promise.all([
    fetch("/api/profile").then((r) => r.json()),
    fetch("/api/stats").then((r) => r.json()),
    fetch("/api/notifications").then((r) => r.json()),
  ]);

  return { profile, stats, notifications };
}
```

---

### Pattern 11: Avoid Layout Thrashing

```typescript
// ❌ bad: repeatedly reads layout between writes
function moveMany(elements: HTMLElement[]) {
  for (const el of elements) {
    const top = el.getBoundingClientRect().top;
    el.style.transform = `translateY(${top}px)`;
  }
}

// ✅ better: read all first, then write
function moveManySafely(elements: HTMLElement[]) {
  const tops = elements.map((el) => el.getBoundingClientRect().top);
  elements.forEach((el, i) => {
    el.style.transform = `translateY(${tops[i]}px)`;
  });
}
```

---

### Pattern 12: Offload Heavy Work to Idle Time

```typescript
declare global {
  interface Window {
    requestIdleCallback?: (cb: () => void) => number;
  }
}

export function scheduleNonUrgentWork(work: () => void): void {
  if (typeof window === "undefined") return;

  if (window.requestIdleCallback) {
    window.requestIdleCallback(work);
    return;
  }

  setTimeout(work, 0);
}
```

---

### Pattern 13: Use `next/image` Correctly

```tsx
import Image from "next/image";

export function Avatar({ src, alt }: { src: string; alt: string }) {
  return <Image src={src} alt={alt} width={64} height={64} priority={false} />;
}
```

---

### Pattern 14: Reduce Payloads with `select`

```typescript
// Fetch only what you need (concept applies to ORMs and APIs)
const res = await fetch("/api/users?fields=id,name");
```

---

## Anti-Patterns (5+)

### Anti-pattern 1: Premature Memoization Everywhere

Overusing `useMemo`/`useCallback` can:

- add overhead
- reduce readability
- create stale dependency bugs

Fix: memoize only when profiling indicates benefit.

---

### Anti-pattern 2: Expensive Computation in Render

```tsx
// ❌ bad: heavy work runs every render
const sorted = items.sort(expensiveComparator);
```

Fix: `useMemo` or precompute in a transition.

---

### Anti-pattern 3: Unbounded Caches

Caches without TTL/eviction become memory leaks.

Fix:

- TTL
- max entries
- explicit invalidation

---

### Anti-pattern 4: “Fetch Waterfalls”

If request B depends on request A, but it doesn’t need to, you’re slowing the page.

Fix:

- parallelize
- restructure endpoints

---

### Anti-pattern 5: Over-Optimizing Images Manually

Avoid hand-rolling responsive srcsets when `next/image` can do it safely.

---

## Real-World Buzz Stack Anchors

Buzz Stack’s docs already include implementation-level optimization patterns:

- concurrency (`useTransition`, `useDeferredValue`) in `docs/PERFORMANCE.md` and `docs/ADVANCED-PATTERNS.md`
- service-layer caching guidance in `docs/PERFORMANCE.md`
- dynamic import example in `docs/PERFORMANCE.md`

Use this skill when you want to turn those insights into concrete changes.

## Cross-References

- Skills:
  - `performance-profiling`: ../performance-profiling/SKILL.md
  - `react-19-patterns`: ../react-19-patterns/SKILL.md
  - `service-layer-caching-patterns`: ../service-layer-caching-patterns/SKILL.md
  - `qa-testing`: ../qa-testing/SKILL.md

- Docs:
  - Performance: ../../../docs/PERFORMANCE.md
  - Advanced patterns: ../../../docs/ADVANCED-PATTERNS.md

## Quick Checklist

- Are expensive updates moved into transitions?
- Is rendering work memoized only when needed?
- Are large lists virtualized?
- Is bundle size reduced via dynamic imports?
- Are requests parallelized/deduped?
- Are caches bounded and invalidation understood?
- Are images handled by `next/image`?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/colten-covington) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
