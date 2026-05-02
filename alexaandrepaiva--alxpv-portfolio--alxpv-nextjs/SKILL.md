---
name: alxpv-nextjs
description: Build and refactor features in the alxpv-portfolio Next.js (App Router) codebase. Use when editing routes under src/app, React components under src/components, Tailwind/shadcn UI, analytics events, or general portfolio site behavior. Use when this capability is needed.
metadata:
  author: alexaandrepaiva
---

# alxpv-nextjs

## Quick start
1. Read `/Users/alexaandrepaiva/alxpv-portfolio/AGENTS.md`.
2. Identify the route/component entrypoint:
   - Routes: `src/app/**/page.tsx`, `src/app/layout.tsx`
   - Layout/UI: `src/components/*`
3. Keep server components by default; add `"use client"` only when required.
4. Use Tailwind + shadcn/ui; use `cn()` from `src/lib/utils.ts`.

## Implementation guardrails
- Prefer minimal changes that match existing structure.
- Don’t introduce new dependencies without adding an ADR in `plans/adr/`.
- If adding UI primitives, prefer using/adding shadcn components under `src/components/ui/` and keep edits minimal.

## Done criteria
- `npm run check` passes.
- No untranslated user-facing strings introduced.
- No console errors in dev.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexaandrepaiva) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
