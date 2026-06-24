---
name: next-best-practices
description: Broad Next.js guidance modeled after common reusable skill packs. Use when this capability is needed.
metadata:
  author: atheory-ai
---

## Use When

Use when adding or restructuring pages in either app.

## Do

- Keep route files under `src/app`.
- Prefer server-rendered pages unless browser state is required.
- Inspect the app dependency and existing route structure before copying patterns.

## Do Not

- Assume sibling apps share the same framework-major behavior.
- Introduce client boundaries for static page composition.

## Example

Both admin and storefront use App Router, but route composition and shared dependencies differ.

## See Also

`../nextjs/SKILL.md`

---
> Source: [atheory-ai/skillex-demo](https://github.com/atheory-ai/skillex-demo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
