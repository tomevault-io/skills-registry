---
name: next-static-site
description: Configure Next.js for static site generation and export. Use when this capability is needed.
metadata:
  author: sinelanguage
---
# Next.js Static Site (SSG)

Generate a fully static site with Next.js and explicit output settings.

## When to Use

- Content-heavy sites with low personalization
- Static hosting requirements

## Inputs

- Routes to pre-render
- Data sources and build-time needs
- Hosting requirements for static export

## Instructions

1. Use static data fetching in App Router or Pages Router.
2. Avoid server-only APIs for static-only routes.
3. Configure `next.config.ts` for static output if required.
4. Validate routing for static-only constraints.

## Output

- Static-ready routing, data fetching, and output configuration.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sinelanguage) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
