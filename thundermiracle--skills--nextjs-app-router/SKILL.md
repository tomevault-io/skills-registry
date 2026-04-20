---
name: nextjs-app-router
description: Build, debug, or migrate Next.js App Router features using the official Next.js documentation. Use when working in the app/ directory, implementing layouts/pages, routing/navigation, route handlers, data fetching, caching/revalidation, metadata, or App Router configuration in next.config.js. Use when this capability is needed.
metadata:
  author: thundermiracle
---

# Next.js App Router

Use the App Router with the official docs as the source of truth. Keep work aligned to the project’s Next.js version and existing routing style. References contain condensed notes and examples derived from the official App Router docs.

## Workflow

1. Confirm App Router usage: look for `app/`, `layout.*`, `page.*`, or `route.*`.
2. Read the Next.js version from `package.json`.
3. Pick the task category below and open the matching reference.
4. Implement changes using file-system conventions.
5. Validate with the project’s dev/test commands if available.

## Core

| Topic | Description | Reference |
| --- | --- | --- |
| Project Structure | Understand `app/`, route segments, and conventions | core-project-structure |
| Layouts & Pages | Apply `layout`, `page`, `template`, `loading`, `error`, `not-found` | core-layouts-pages |
| Routing & Navigation | Use file-system routing, dynamic segments, and navigation | core-routing-navigation |
| Server & Client Components | Decide boundaries and apply `"use client"` only when needed | core-server-client-components |
| Data Fetching | Use async Server Components and built-in fetch patterns | core-data-fetching |
| Caching & Revalidation | Use caching defaults and revalidation controls | core-caching-revalidation |
| Error Handling | Use `error` and `not-found` patterns consistently | core-error-handling |
| Metadata & OG | Set static/dynamic metadata and OG images | core-metadata |
| Route Handlers | Implement `route.*` handlers and HTTP methods | core-route-handlers |
| Configuration | Update `next.config.js` for App Router behavior | core-config |

## Rules of Thumb

- Prefer Server Components by default; add `"use client"` only when required.
- Avoid Pages Router APIs (`getServerSideProps`, `getStaticProps`, `next/router`) unless explicitly requested.
- Follow file-system conventions instead of custom routing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thundermiracle) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
