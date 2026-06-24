---
name: routing-guide
description: Client-side routing patterns, lazy loading, and navigation structure. Keywords: routing, navigation, react router, lazy loading, nested routes. Use when this capability is needed.
metadata:
  author: willyu1007
---

# Routing Guide

This skill provides routing patterns for frontend applications.

---

## 1. Principles

- Keep routing structure predictable and discoverable.
- Lazy-load route-level components when they are heavy.
- Keep route params validated and typed where possible.

---

## 2. Nested routing (common pattern)

- Prefer nested routes for step-by-step flows and detail pages.
- Keep shared layout components at a parent route level to avoid remount churn.

---

## 3. Links and navigation

- Prefer declarative navigation (`<Link>` components) over imperative navigation when possible.
- Ensure active states and breadcrumbs are consistent (if used).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
