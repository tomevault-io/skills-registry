---
name: react-performance-review
description: Review React applications for performance issues and optimization opportunities. Use when analyzing React code for: (1) Concurrency issues (useTransition, useDeferredValue, Suspense), (2) useEffect problems (missing deps, cleanup, unnecessary effects), (3) Memoization (useMemo, useCallback, React.memo misuse), (4) Re-render optimization, (5) State management inefficiencies, (6) Bundle size and code splitting. Trigger on requests like 'review React performance', 'optimize React app', 'find React bottlenecks', 'audit React code'. Use when this capability is needed.
metadata:
  author: thanhtunguet
---

# React Performance Review

Review React code systematically for performance anti-patterns and optimization opportunities.

## Review Checklist

### 1. Concurrency (React 18+)

**Check for:**
- CPU-intensive renders blocking UI → wrap with `useTransition`
- Stale search/filter results → use `useDeferredValue`
- Missing Suspense boundaries for lazy components
- Blocking navigation transitions

```tsx
// BAD: Blocks UI during expensive filter
const filtered = items.filter(expensiveFilter);

// GOOD: Non-blocking with transition
const [isPending, startTransition] = useTransition();
const [filtered, setFiltered] = useState(items);
startTransition(() => setFiltered(items.filter(expensiveFilter)));
```

### 2. useEffect Issues

**Check for:**
- Missing/incorrect dependency arrays
- Missing cleanup for subscriptions, timers, AbortController
- Effects that should be event handlers
- Effects that derive state (use useMemo instead)
- Cascading effects (effect → setState → effect)

```tsx
// BAD: Derived state in effect
useEffect(() => {
  setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);

// GOOD: Compute during render
const fullName = `${firstName} ${lastName}`;

// BAD: Missing cleanup
useEffect(() => {
  const id = setInterval(tick, 1000);
}, []); // Memory leak!

// GOOD: Proper cleanup
useEffect(() => {
  const id = setInterval(tick, 1000);
  return () => clearInterval(id);
}, []);
```

### 3. Memoization (useMemo/useCallback/React.memo)

**Check for:**
- Missing memoization for expensive computations passed as props
- Unnecessary memoization (primitives, simple operations)
- Incorrect dependency arrays breaking memoization
- React.memo without stable props

```tsx
// UNNECESSARY: Simple operation
const doubled = useMemo(() => count * 2, [count]); // Just use: count * 2

// NECESSARY: Expensive computation passed to memoized child
const sorted = useMemo(() =>
  items.sort((a, b) => complexCompare(a, b)),
  [items]
);

// BAD: Inline function breaks memo
<MemoizedChild onClick={() => handleClick(id)} />

// GOOD: Stable callback reference
const handleItemClick = useCallback(() => handleClick(id), [id]);
<MemoizedChild onClick={handleItemClick} />
```

### 4. Re-render Optimization

**Check for:**
- Components re-rendering when props haven't meaningfully changed
- Context providers causing wide re-renders
- Missing keys or incorrect key usage in lists
- State stored too high in component tree

```tsx
// BAD: Entire tree re-renders on theme change
<AppContext.Provider value={{ user, theme, settings }}>

// GOOD: Split contexts by update frequency
<UserContext.Provider value={user}>
  <ThemeContext.Provider value={theme}>
```

### 5. State Management

**Check for:**
- Prop drilling through many levels → consider context or state library
- Redundant state (derivable from other state/props)
- State updates batching issues (pre-React 18 patterns)
- Storing derived data in state

### 6. Bundle & Loading

**Check for:**
- Large components not code-split with `React.lazy`
- Missing Suspense fallbacks
- Heavy dependencies imported synchronously
- Images/assets not lazy loaded

```tsx
// BAD: Loads chart library on initial bundle
import { Chart } from 'heavy-chart-lib';

// GOOD: Lazy load when needed
const Chart = lazy(() => import('heavy-chart-lib').then(m => ({ default: m.Chart })));
```

## Review Output Format

Report findings in this structure:

```markdown
## React Performance Review: [Component/File]

### Critical Issues
- [Issue]: [Location] - [Impact] - [Fix]

### Warnings
- [Issue]: [Location] - [Impact] - [Fix]

### Suggestions
- [Optimization opportunity]: [Location] - [Potential benefit]

### Summary
[Brief assessment of overall performance health]
```

## References

For detailed patterns and edge cases, see:
- [references/patterns.md](references/patterns.md) - Comprehensive anti-patterns with fixes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thanhtunguet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
