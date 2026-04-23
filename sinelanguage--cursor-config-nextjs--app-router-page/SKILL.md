---
name: next-app-router-page
description: Scaffold a Next.js App Router page with server-first rendering. Use when this capability is needed.
metadata:
  author: sinelanguage
---
# Next.js App Router Page

Create a new App Router route with Server Components by default and explicit
route boundaries.

## When to Use

- New pages under `app/`
- Server-first rendering with partial hydration
- Routes requiring metadata and loading/error UX

## Inputs

- Route segment path (e.g. `app/dashboard`)
- Data needs (static/ISR/dynamic)
- Client interactivity requirements
- Metadata requirements (title/description/OG)

## Instructions

1. Create `page.tsx` in the route segment.
2. Default to a Server Component (no `"use client"`).
3. Add `loading.tsx`, `error.tsx`, and `not-found.tsx` per segment.
4. Use `fetch` with explicit caching (`no-store` or `revalidate`).
5. Add `generateMetadata` or `metadata` for SEO.
6. Add `Route Segment Config` (runtime, revalidate) when needed.
7. Split interactivity into leaf Client Components.

## Output

- App Router files in the target segment with server-first patterns.
- Explicit data and caching strategy for the route.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sinelanguage) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
