---
name: modern-best-practice-nextjs
description: Build modern Next.js apps with App Router and best practices Use when this capability is needed.
metadata:
  author: ddoman90
---

# Next.js Best Practices (App Router)

Next.js changes frequently - use web search or Context7 MCP (via DocsExplorer agent) for current documentation.

## Routing & Structure

- Use the App Router in `app/` for new work
- Use nested layouts and route groups to organize sections
- Keep server components as the default; add `'use client'` only where needed

## Data Fetching & Caching

- Fetch data in React Server Components
- **AVOID** fetching via `useEffect()` and `fetch()`
- Use server actions ("Server Functions") for mutations, with React Hooks like `useActionState`

## UI States

- Provide `loading.tsx` and `error.tsx` for route-level UX
- Use `Suspense` boundaries around async UI

## Metadata & SEO

- Use the Metadata API in layouts and pages
- Prefer static metadata when possible; keep dynamic metadata minimal

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ddoman90) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
