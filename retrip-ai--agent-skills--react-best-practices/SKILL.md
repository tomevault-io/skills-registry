---
name: react-best-practices
description: React performance optimization guidelines focused on Tanstack Start applications. Use when writing, reviewing, or refactoring React code with Tanstack Start, Tanstack Query, and Tanstack Router. Triggers on React components, data fetching, bundle optimization, performance improvements, code review, or refactoring tasks. Use when this capability is needed.
metadata:
  author: retrip-ai
---

# React Best Practices

## Overview

Comprehensive performance optimization guide for React applications, containing 37+ rules across 8 categories. Rules are prioritized by impact to guide automated refactoring and code generation.

## When to Apply

Reference these guidelines when:

* Writing new React components or pages
* Implementing data fetching (client or server-side)
* Reviewing code for performance issues
* Refactoring existing React code
* Optimizing bundle size or load times

## Priority-Ordered Guidelines

Rules are prioritized by impact:

| Priority | Category | Impact |
|----------|----------|--------|
| 1 | Eliminating Waterfalls | CRITICAL |
| 2 | Bundle Size Optimization | CRITICAL |
| 3 | Server-Side Performance | HIGH |
| 4 | Client-Side Data Fetching | MEDIUM-HIGH |
| 5 | Re-render Optimization | MEDIUM |
| 6 | Rendering Performance | MEDIUM |
| 7 | JavaScript Performance | LOW-MEDIUM |
| 8 | Advanced Patterns | LOW |

## Quick Reference

### Critical Patterns (Apply First)

**Eliminate Waterfalls:**

* Defer await until needed (move into branches)
* Use `Promise.all()` for independent async operations
* Start promises early, await late
* Use `better-all` for partial dependencies
* Use Suspense boundaries to stream content

**Reduce Bundle Size:**

* Avoid barrel file imports (import directly from source)
* Use `React.lazy()` and dynamic imports for heavy components
* Defer non-critical third-party libraries
* Preload based on user intent

### High-Impact Server Patterns

* Use Tanstack Query for request deduplication and caching
* Use LRU cache for cross-request caching
* Minimize data serialization between server and client
* Parallelize data fetching with component composition and Tanstack Query

### Medium-Impact Client Patterns

* Use Tanstack Query for automatic request deduplication
* Defer state reads to usage point
* Use lazy state initialization for expensive values
* Use derived state subscriptions
* Apply `startTransition` for non-urgent updates

### Rendering Patterns

* Animate SVG wrappers, not SVG elements directly
* Use `content-visibility: auto` for long lists
* Prevent hydration mismatch with inline scripts
* Use explicit conditional rendering (`? :` not `&&`)

### JavaScript Patterns

* Batch DOM CSS changes via classes
* Build index maps for repeated lookups
* Cache repeated function calls
* Use `toSorted()` instead of `sort()` for immutability
* Early length check for array comparisons

## References

Full documentation with code examples is available in:

* `references/react-performance-guidelines.md` - Complete guide with all patterns
* `references/tanstack-patterns.md` - Tanstack Start, Query, and Router specific patterns
* `references/rules/` - Individual rule files organized by category

To look up a specific pattern, grep the rules directory:

```
grep -l "suspense" references/rules/
grep -l "barrel" references/rules/
grep -l "swr" references/rules/
```

## Rule Categories in `references/rules/`

* `async-*` - Waterfall elimination patterns
* `bundle-*` - Bundle size optimization
* `server-*` - Server-side performance
* `client-*` - Client-side data fetching
* `rerender-*` - Re-render optimization
* `rendering-*` - DOM rendering performance
* `js-*` - JavaScript micro-optimizations
* `advanced-*` - Advanced patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/retrip-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
