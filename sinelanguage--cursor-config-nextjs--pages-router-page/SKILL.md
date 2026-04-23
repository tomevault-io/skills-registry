---
name: next-pages-router-page
description: Scaffold a Pages Router route with data fetching and tests. Use when this capability is needed.
metadata:
  author: sinelanguage
---
# Next.js Pages Router Page

Create a new `pages/` route for legacy or compatibility use cases with explicit
data fetching and caching.

## When to Use

- Existing `pages/` routes
- Legacy integrations that require Pages Router

## Inputs

- Route path (e.g. `pages/account.tsx`)
- Data fetching method (SSR/SSG/ISR)
- Revalidation needs (if SSG/ISR)

## Instructions

1. Create the page file under `pages/`.
2. Use `getServerSideProps`, `getStaticProps`, or `getStaticPaths` as needed.
3. Keep data fetching typed and validated.
4. Add `revalidate` for ISR when applicable.
5. Add a basic test if testing is configured.

## Output

- Page file with typed data fetching and caching for Pages Router.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sinelanguage) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
