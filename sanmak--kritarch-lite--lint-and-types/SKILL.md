---
name: lint-and-types
description: Use when making TypeScript or ESLint-sensitive changes in `app/`, `components/`, or `lib/`.
metadata:
  author: sanmak
---

# Goal

Keep TypeScript types and ESLint clean across the app.

# Do

- Run `npm run lint` after code changes in the root app.
- Avoid `any` unless truly necessary; keep types explicit and local.

# Don't

- Leave new warnings or type gaps unaddressed.

# Examples

- "Refactor a component and re-run lint."
- "Adjust API types in `lib/`."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sanmak) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
