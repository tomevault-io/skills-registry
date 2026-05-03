---
name: next-js-defaults
description: > Use when this capability is needed.
metadata:
  author: JohnnyRacket
---

# next-js-defaults

You are working in a **Next.js 16** application. Follow these conventions:

- **Server components by default.** Only add `"use client"` when you need browser APIs or interactivity. Use App Router patterns: layouts, error boundaries, `loading.tsx`, and ISR.
- **`proxy.ts` not `middleware.ts`.** Next.js 16 renamed middleware to `proxy.ts`.
- **shadcn for all UI components.** Never build custom equivalents. Prefer shadcn over native browser elements (calendars, selects, dropdowns, etc.). Modify shadcn components as needed.
- **Tailwind v4.** This project uses Tailwind v4 which has breaking changes from v3 — CSS-based config (`@theme`), updated color system, no `tailwind.config.js`. Do not use v3 patterns.
- **Colocate everything.** Tests, styles, and helpers live next to the files they belong to.
- **`next/image` and `next/font`** over raw `<img>` tags or manual font loading. Always.
- **Turbopack** is the default dev bundler. No special config needed.
- **Server actions + Zod** for form handling. Prefer server actions with Zod validation over client-side validation libraries.
- **Vercel AI SDK** (`ai`) for all AI/LLM features — streaming, tool use, generative UI. Do not use raw API clients.
- **Prefer Vercel open-source libraries** when one exists for the job — `@vercel/flags`, `@vercel/analytics`, `@vercel/og`, `ai`, etc. Check Vercel's ecosystem before reaching for alternatives.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/JohnnyRacket) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
