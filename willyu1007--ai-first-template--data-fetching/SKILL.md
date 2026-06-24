---
name: data-fetching
description: Data fetching, caching, and invalidation patterns for frontend apps. Keywords: data fetching, api, caching, query, mutation, react query. Use when this capability is needed.
metadata:
  author: willyu1007
---

# Data Fetching

This skill describes patterns for client-side data fetching and caching.

---

## 1. Core rules

1. Prefer a single data access layer (API client + feature APIs).
2. Use stable query keys for caching.
3. Invalidate caches intentionally on mutations.
4. Keep loading/error UX consistent (Suspense or explicit states).

---

## 2. Query key pattern

Example shape:
- `["users", "list", filters]`
- `["users", "detail", userId]`

Avoid:
- keys that depend on unstable object identity
- mixing unrelated resources in a single key

---

## 3. Mutation pattern

After a successful mutation:
- invalidate affected list queries
- update detail caches when possible

---

## 4. Error handling

- Show user-friendly errors.
- Capture exceptions to monitoring with route/feature context (when available).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
