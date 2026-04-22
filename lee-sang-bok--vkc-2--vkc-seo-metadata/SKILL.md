---
name: vkc-seo-metadata
description: Standardize Next.js metadata in VKC (generateMetadata, buildPageMetadata, canonical, alternates, OG, Twitter, robots/noindex). Use when changing SEO, sitemap, robots, or social previews. (키워드= SEO, 메타데이터, 캐노니컬, alternates, hreflang, 오픈그래프, robots, noindex, sitemap) Use when this capability is needed.
metadata:
  author: lee-sang-bok
---

# VKC SEO Metadata (generateMetadata)

## When to use

- You touch `generateMetadata` or page metadata behavior.
- You need `canonical` / `alternates` (hreflang) alignment for i18n routes.
- You change OpenGraph(OG) / Twitter previews.
- You change `src/app/sitemap.ts` or `src/app/robots.ts`.
- You need `robots` control (e.g. `noindex`, gated pages, admin-only pages).

## Hard rules (this repo)

- Prefer `buildPageMetadata` from `src/lib/seo/metadata.ts` to avoid drift.
- Always set:
  - `alternates.canonical`
  - `alternates.languages`
- If a page should not be indexed, set `robots` in metadata (do not rely on ad-hoc tags).

## Canonical references

- Metadata builder: `src/lib/seo/metadata.ts` (`buildPageMetadata`)
- Sitemap: `src/app/sitemap.ts`
- Robots: `src/app/robots.ts`

## References

- Spec & checklist: `.codex/skills/vkc-seo-metadata/references/metadata-spec.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lee-sang-bok) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
