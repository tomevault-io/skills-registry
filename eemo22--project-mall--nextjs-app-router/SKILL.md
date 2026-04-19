---
name: nextjs-app-router
description: Build and maintain Next.js App Router features with server-first patterns, explicit cache strategy, Route Handlers, and Server Actions. Use when creating or changing routes, layouts, server actions, auth boundaries, or data caching behavior. Use when this capability is needed.
metadata:
  author: eemo22
---

# Next.js App Router

- Start from server-first design and move client logic to isolated interactive islands.
- Keep data writes in Server Actions unless an external HTTP API contract is required.
- Use Route Handlers only for external API boundaries and webhook style integrations.
- Keep cache intent explicit and pair every mutation with targeted revalidation.
- Place auth and role checks close to the write boundary.
- Reuse logic from `packages/domain` and `packages/db` instead of duplicating.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eemo22) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
