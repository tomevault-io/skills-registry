---
name: next-data-fetching-cache
description: Define data fetching and caching strategy for Next.js routes. Use when this capability is needed.
metadata:
  author: sinelanguage
---
# Next.js Data Fetching & Cache Strategy

Plan data fetching and caching for App Router routes with explicit freshness
requirements.

## When to Use

- Designing new data-heavy pages
- Choosing between SSR, SSG, and ISR

## Inputs

- Data freshness needs
- User personalization requirements
- Revalidation and cache tagging strategy

## Instructions

1. Decide per-route caching (`no-store` vs `revalidate`).
2. Use server-side fetching to avoid client waterfalls.
3. Use tags and on-demand revalidation where needed.
4. Document cache decisions and staleness tolerance.

## Output

- Clear cache and fetch strategy for the route.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sinelanguage) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
