---
name: performance-hint
description: Notices performance issues when viewing code with nested loops, database queries in loops, inefficient algorithms, or missing caching. Use when user shows code that might have O(n²) complexity, N+1 queries, or obvious performance bottlenecks. Use when this capability is needed.
metadata:
  author: alexeytriplea
---

# Performance Hint

Lightweight performance awareness for conversations. Provides quick hints about potential bottlenecks without deep analysis.

## When to activate

Activate when you notice in code being discussed:
- Nested loops (O(n²) or worse)
- Database queries inside loops (N+1 problem)
- Sorting or filtering inside loops
- Sequential API calls that could be parallel
- Large arrays without pagination
- Missing memoization for expensive operations
- Event listeners without cleanup

## What to do

**Briefly mention** the issue in 1-2 sentences, then suggest running a command:

**Example responses:**

```
"This nested loop creates O(n²) complexity on lines 45-52.
Run `/review src/utils.js` to check for performance optimizations."
```

```
"I see a query inside a loop (N+1 problem) on line 28. This will make 100+ database calls.
Run `/deep-review` to analyze performance and get optimization suggestions."
```

```
"These API calls are sequential (lines 15-18) but could run in parallel with Promise.all.
Run `/quick-check src/api/` for performance check."
```

## Important rules

- ❌ Do NOT perform full complexity analysis
- ❌ Do NOT benchmark or profile code
- ✅ Only mention obvious issues in current conversation
- ✅ Keep hints brief (1-2 sentences)
- ✅ Always suggest a command for detailed analysis
- ✅ Focus on impactful optimizations, not micro-optimizations

## Red flags to watch for

**Algorithm complexity:**
- Nested loops, especially with array methods like `.find()` or `.filter()`
- Sorting inside loops

**Database:**
- Queries in `for`/`forEach` loops
- Missing `include` or eager loading
- No pagination on `.findAll()`

**Memory:**
- Event listeners without removal
- Timers/intervals not cleared
- Large objects in closures

**Network:**
- Multiple `await` calls that could use `Promise.all`
- No request debouncing
- Fetching entire datasets

## Commands to suggest

- `/quick-check` - for fast performance scan
- `/review [file]` - for specific file analysis
- `/deep-review` - for comprehensive performance review with optimization strategies

This is a hint tool, not a replacement for proper performance profiling.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexeytriplea) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
