---
name: next-data-rendering
description: Use for Next.js App Router work in this repo when refactoring page data flow, reducing client fetch-on-mount patterns, introducing server entrypoints with client islands, or moving list/search/pagination state to searchParams. Use when this capability is needed.
metadata:
  author: d-velopers-com
---

# Next Data Rendering

Use this skill for route and component work in this repo.

## Default approach

- Make the route entrypoint (`page.tsx`, `layout.tsx`) the data-loading boundary.
- Keep client components for interactivity only.
- Pass serialized `initial*` props from the server entrypoint to client islands.
- Use `searchParams` as the source of truth for filters, search, tabs, and pagination.

## Repo rules

- Do not fetch `/api/*` from a server component if the same data can be read through `lib/` or `features/`.
- Do not mount a page empty and then populate it with `useEffect`.
- Avoid duplicating permission/config fetches in navbar and page-level client components.
- Prefer `router.replace(..., { scroll: false })` or `router.refresh()` from client islands over custom client-side bootstrap fetches.

## Apply here

- `/`, `/jobs`, `/dashboard`, `/jobs/manage`, and `/users/[handler]` should be server entrypoints with adjacent `*-client.tsx` files when interactivity is needed.
- Keep mutations in route handlers; keep initial reads in server code.

## References

- Read `.codex/skills/next-data-rendering/references/repo-patterns.md` before touching route data flow.

---
> Source: [d-velopers-com/d-velopers-website](https://github.com/d-velopers-com/d-velopers-website) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
