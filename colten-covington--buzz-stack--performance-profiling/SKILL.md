---
name: performance-profiling
description: React profiling, bundle analysis, runtime performance measurement, and Web Vitals optimization for Buzz Stack. Use when this capability is needed.
metadata:
  author: colten-covington
---

# Performance Profiling & Optimization

## Overview

Performance is a feature. This skill covers identifying bottlenecks using tools, understanding root causes, and implementing targeted optimizations without premature over-engineering.

**Why it matters:**

- Slow apps lose users (UX → Trust → Revenue)
- Large bundles cost bandwidth and money
- Slow API calls block UI (poor perceived performance)
- Web Vitals directly affect SEO rankings

## Core Concepts

### 1. Web Vitals (What Users Feel)

```typescript
// Modern performance metrics (Core Web Vitals)
interface CoreWebVitals {
  LCP: number; // Largest Contentful Paint (< 2.5s) ✓
  FID: number; // First Input Delay (< 100ms) ✓
  CLS: number; // Cumulative Layout Shift (< 0.1) ✓
}

// How to measure
function initWebVitalsTracking() {
  // Largest Contentful Paint
  const observer = new PerformanceObserver((list) => {
    const entries = list.getEntries();
    const lastEntry = entries[entries.length - 1];
    console.log("LCP:", lastEntry.renderTime || lastEntry.loadTime);
  });
  observer.observe({ entryTypes: ["largest-contentful-paint"] });

  // First Input Delay
  new PerformanceObserver((list) => {
    for (const entry of list.getEntries()) {
      console.log("FID:", entry.processingDuration);
    }
  }).observe({ entryTypes: ["first-input"] });

  // Cumulative Layout Shift
  let clsValue = 0;
  new PerformanceObserver((list) => {
    for (const entry of list.getEntries()) {
      if (!entry.hadRecentInput) {
        clsValue += entry.value;
        console.log("CLS:", clsValue);
      }
    }
  }).observe({ entryTypes: ["layout-shift"] });
}
```

**Good targets:**

- LCP < 2.5 seconds (when largest content visible)
- FID < 100 milliseconds (responsive to input)
- CLS < 0.1 (no surprise layout jumps)

### 2. React Performance Profiling

```typescript
// Use React DevTools Profiler
// Steps:
// 1. Open React DevTools
// 2. Go to "Profiler" tab
// 3. Record interactions
// 4. Analyze flame graph

// What to look for in Profiler:
// - Render time per component (hover for details)
// - Which components re-rendered (why?)
// - Render count (should be minimal)
// - Props changes (causing unnecessary re-renders?)

// Example: Identify bad rendering
function SearchResults({ items, query }) {
  // ❌ ANTI-PATTERN: Recalculates every render
  const results = items
    .map(item => ({
      ...item,
      score: calculateScore(item, query) // Expensive!
    }))
    .filter(item => item.score > 0)
    .sort((a, b) => b.score - a.score);

  return <ResultsList items={results} />;
}

// ✅ FIX: Memoize expensive calculation
function SearchResults({ items, query }) {
  const results = useMemo(() => {
    return items
      .map(item => ({
        ...item,
        score: calculateScore(item, query)
      }))
      .filter(item => item.score > 0)
      .sort((a, b) => b.score - a.score);
  }, [items, query]); // Only recalculate when inputs change

  return <ResultsList items={results} />;
}
```

### 3. Bundle Analysis

```bash
# Analyze bundle size
npm run build

# View bundle breakdown
webpack-bundle-analyzer --report

# What to look for:
# - Which packages are largest?
# - Are there duplicates?
# - Can we code-split or lazy-load?
```

**Common optimizations:**

```typescript
// ❌ WRONG: Import entire library
import _ from 'lodash'; // 71 KB

// ✅ CORRECT: Import only what you need
import { debounce } from 'lodash-es'; // 5 KB

// ❌ WRONG: Load everything upfront
import HeavyChart from '@/components/HeavyChart';

// ✅ CORRECT: Code-split with dynamic import
const HeavyChart = dynamic(() => import('@/components/HeavyChart'), {
  loading: () => <Skeleton />,
});
```

### 4. Runtime Performance Tools

```typescript
// Performance API (built-in)
function measureOperation(name: string, fn: () => void) {
  performance.mark(`${name}-start`);
  fn();
  performance.mark(`${name}-end`);
  performance.measure(name, `${name}-start`, `${name}-end`);

  const measure = performance.getEntriesByName(name)[0];
  console.log(`${name}: ${measure.duration.toFixed(2)}ms`);
}

// Usage
measureOperation("search", () => {
  performSearch(query);
});
// Output: search: 45.23ms

// Monitor function calls
function monitorCalls(fn: Function, name: string) {
  let callCount = 0;
  return function (...args: unknown[]) {
    callCount++;
    const start = performance.now();
    const result = fn.apply(this, args);
    const duration = performance.now() - start;
    console.log(`${name} (call #${callCount}): ${duration.toFixed(2)}ms`);
    return result;
  };
}

// Usage
const monitored = monitorCalls(calculateScore, "calculateScore");
monitored({ name: "item" }, "query");
// Output: calculateScore (call #1): 0.45ms
```

## Deep Patterns

### Pattern 1: Render Performance Optimization

```typescript
// Problem: Expensive render function
function DataTable({ data, sortKey, filterText }) {
  // This entire calculation happens on every render
  const sorted = [...data].sort((a, b) =>
    a[sortKey].localeCompare(b[sortKey])
  );

  const filtered = sorted.filter(row =>
    row.name.includes(filterText)
  );

  return (
    <table>
      {filtered.map(row => <TableRow key={row.id} data={row} />)}
    </table>
  );
}

// Solution: Memoize intermediate results
function DataTable({ data, sortKey, filterText }) {
  // Memoize sorting
  const sorted = useMemo(
    () => [...data].sort((a, b) =>
      a[sortKey].localeCompare(b[sortKey])
    ),
    [data, sortKey]
  );

  // Memoize filtering (depends on sorted)
  const filtered = useMemo(
    () => sorted.filter(row => row.name.includes(filterText)),
    [sorted, filterText]
  );

  // Memoize callbacks
  const renderRow = useCallback(
    (row) => <TableRow key={row.id} data={row} />,
    []
  );

  return (
    <table>
      {filtered.map(renderRow)}
    </table>
  );
}
```

### Pattern 2: API Call Optimization

```typescript
// Problem: Multiple API calls for same data
function SearchFeature({ query }) {
  const [results1, setResults1] = useState([]);
  const [results2, setResults2] = useState([]);

  useEffect(() => {
    // Called whenever query changes
    fetch(`/api/search?q=${query}`)
      .then((r) => r.json())
      .then(setResults1);
    fetch(`/api/search?q=${query}`)
      .then((r) => r.json())
      .then(setResults2); // Duplicate!
  }, [query]);
}

// Solution: Deduplicate with service layer
class SearchService {
  private cache = new Map<string, never[]>();
  private pending = new Map<string, Promise<never[]>>();

  async search(query: string) {
    // Return cached result if available
    if (this.cache.has(query)) {
      return this.cache.get(query)!;
    }

    // Return pending request if already in flight
    if (this.pending.has(query)) {
      return this.pending.get(query)!;
    }

    // Fetch and cache
    const promise = fetch(`/api/search?q=${query}`).then((r) => r.json());
    this.pending.set(query, promise);
    const result = await promise;
    this.cache.set(query, result);
    this.pending.delete(query);

    return result;
  }
}

// Usage
const searchService = new SearchService();

function SearchFeature({ query }) {
  const [results, setResults] = useState([]);

  useEffect(() => {
    searchService.search(query).then(setResults);
  }, [query]);
}
```

### Pattern 3: List Virtualization (Large Data Sets)

```typescript
// Problem: Rendering 10,000 items = slow
function LargeList({ items }) {
  return (
    <div>
      {items.map(item => (
        <ExpensiveComponent key={item.id} item={item} />
      ))}
    </div>
  );
}

// Solution: Only render visible items
import { FixedSizeList as List } from 'react-window';

function LargeList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>
      <ExpensiveComponent item={items[index]} />
    </div>
  );

  return (
    <List
      height={600}
      itemCount={items.length}
      itemSize={35}
      width="100%"
    >
      {Row}
    </List>
  );
}
// Result: Only ~20 items rendered at a time, 50x faster
```

### Pattern 4: Network Waterfall Reduction

```typescript
// Problem: Sequential requests (slow)
async function loadUserProfile(userId: string) {
  const user = await fetch(`/api/users/${userId}`).then((r) => r.json());
  const posts = await fetch(`/api/users/${userId}/posts`).then((r) => r.json());
  const comments = await fetch(`/api/users/${userId}/comments`).then((r) =>
    r.json(),
  );
}
// Waterfall: Request 1 → Response 1 → Request 2 → Response 2 → Request 3

// Solution: Parallel requests
async function loadUserProfile(userId: string) {
  const [user, posts, comments] = await Promise.all([
    fetch(`/api/users/${userId}`).then((r) => r.json()),
    fetch(`/api/users/${userId}/posts`).then((r) => r.json()),
    fetch(`/api/users/${userId}/comments`).then((r) => r.json()),
  ]);
}
// Parallel: All 3 requests at once (3x faster)
```

## Anti-Patterns to Avoid

```typescript
// ❌ Wrong: Premature optimization
const optimized = useMemo(() => simple.toUpperCase(), [simple]);

// ✅ Correct: Profile first, optimize second
// Only useMemo if you've confirmed it's slow

// ❌ Wrong: Rendering large lists without virtualization
{items.map(item => <Component item={item} />)} // 1000 items = 1000 DOM nodes

// ✅ Correct: Virtualize for large lists
<react-window.FixedSizeList items={items} />

// ❌ Wrong: Ignoring Web Vitals
// Ship with slow LCP, high CLS, high FID

// ✅ Correct: Monitor and budget
// LCP < 2.5s, FID < 100ms, CLS < 0.1
```

## Profiling Checklist

Before optimization:

- [ ] Identify the bottleneck (React, Network, Bundle?)
- [ ] Measure baseline (current performance)
- [ ] Use appropriate tool (Profiler, Network, Lighthouse)
- [ ] Understand root cause (why is it slow?)

During optimization:

- [ ] Change ONE thing at a time
- [ ] Measure improvement
- [ ] Confirm improvement matches theory

## Real-World Metrics Goals

| Metric          | Good    | Would Be Great |
| --------------- | ------- | -------------- |
| **LCP**         | < 2.5s  | < 1.5s         |
| **FID**         | < 100ms | < 50ms         |
| **CLS**         | < 0.1   | < 0.05         |
| **Bundle Size** | < 200KB | < 100KB        |
| **First Paint** | < 1s    | < 500ms        |

## Tools Available

- **React DevTools Profiler** - Component render times
- **Chrome DevTools** - Network, Performance, Coverage
- **Lighthouse** - Comprehensive audit with scores
- **WebPageTest** - Detailed waterfall analysis
- **Bundle Analyzer** - Dependency visualization

## Key Questions

1. **Which metric is slowest?** (LCP? FID? CLS?)
2. **Which component is expensive?** (Profile in React DevTools)
3. **Is the API slow?** (Check Network tab)
4. **Is the bundle too large?** (Analyze with webpack-bundle-analyzer)
5. **Have I measured improvement?** (Otherwise, you're guessing)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/colten-covington) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
