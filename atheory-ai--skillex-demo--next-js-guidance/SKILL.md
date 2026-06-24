---
name: next-js-guidance
description: Baseline guidance for App Router apps. Use when this capability is needed.
metadata:
  author: atheory-ai
---

## Use When

Use this for broad App Router orientation.

## Do

- Put route pages under `src/app`.
- Keep page components server-rendered unless interactivity requires client components.
- Read the app package before assuming framework behavior.

## Do Not

- Mix conventions between sibling apps just because both use Next.js.
- Add client components for static merchandising or dashboard content.

## Example

`apps/admin/src/app/settings/page.tsx` and `apps/storefront/src/app/promotions/page.tsx` are both App Router pages, but they target different Next majors.

## See Also

Use app-local Next skills for version-specific guidance.

---
> Source: [atheory-ai/skillex-demo](https://github.com/atheory-ai/skillex-demo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
