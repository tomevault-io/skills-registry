---
name: community
description: Add new language to a Next.js + next-intl application Use when this capability is needed.
metadata:
  author: beel-collab
---

This is a guide to add a new language to a Next.js project using next-intl for internationalization,

- For i18n, the application uses next-intl.
- All translations are in the directory `./messages`.
- The UI component is `src/components/language-toggle.tsx`.
- Routing and middleware configuration are handled in:
  - `src/i18n/routing.ts`
  - `src/middleware.ts`

When adding a new language:

- Translate all the content of `en.json` to the new language. The goal is to have all the JSON entries in the new language for a complete translation.
- Add the path in `routing.ts` and `middleware.ts`.
- Add the language to `language-toggle.tsx`.

---
> Source: [beel-collab/presets.dev](https://github.com/beel-collab/presets.dev) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
