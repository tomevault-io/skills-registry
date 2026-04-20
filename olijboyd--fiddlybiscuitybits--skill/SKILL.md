---
name: dashboard-layout
description: Use when building dashboard page layouts with sidebar navigation, header, and
metadata:
  author: olijboyd
---

# Dashboard Layout Patterns

## When to Use
- Creating new dashboard pages
- Adding sidebar navigation sections
- Building responsive grid layouts for data panels

## Instructions
1. Use the existing DashboardShell component as the page wrapper
2. Sidebar items defined in src/config/navigation.ts
3. Grid layouts use CSS Grid with Tailwind: grid-cols-1 md:grid-cols-2 lg:grid-cols-3
4. All panels should have loading skeletons
5. Mobile: sidebar collapses to hamburger menu

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/olijboyd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
