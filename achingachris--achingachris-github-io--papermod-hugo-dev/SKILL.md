---
name: papermod-hugo-dev
description: Develop and maintain the Chris Achinga Hugo site built on PaperMod—use when adding/editing posts, adjusting PaperMod params/menus in hugo.yaml, tweaking styling/partials/assets, enabling features (search, archives, profile/home modes), or debugging Hugo builds. Use when this capability is needed.
metadata:
  author: achingachris
---

# PaperMod Hugo Dev

## Overview

Guide for developing, fixing, and styling this PaperMod-based Hugo site. Covers content workflows, configuration, feature toggles, custom layout/SEO overrides, and validation.

## Quick Start

- Prereq: Hugo Extended v0.147+ (`hugo version` should include `extended`).
- Dev: `hugo server -D` (includes drafts) at http://localhost:1313; published-only: `hugo server`.
- Build check: `hugo --gc --minify` before handoff to catch template/front matter issues.
- Generated outputs: avoid editing `public/` and `resources/`; theme overrides live in `layouts/` and `assets/`.

## Repo Map (local)

- `hugo.yaml`: site + PaperMod params (menus, defaultTheme, search outputs, etc.).
- `content/posts/`: main section (Page Bundles recommended). `.drafts/` holds scratch notes; move into `content/posts/` to publish.
- `archetypes/default.md`: default front matter.
- `layouts/shortcodes/articleCount.html`: counts non-draft pages in `params.mainsections` (used on homepage).
- `layouts/partials/header.html`: custom fixed nav + theme toggle; renders `Site.Menus.main`.
- `layouts/partials/extend_head.html`: fonts, OG/Twitter meta, canonical, default `images/OG_IMAGE.png`.
- `layouts/partials/footer.html` + `extend_footer.html`: footer links, Cabin analytics, a11y font scale/contrast, theme persistence.
- `layouts/_default/list.html`: tweaked list layout, breadcrumbs, pagination; `layouts/_default/_markup/render-image.html`: lazy `<img>` render hook.
- `assets/css/extended/custom.css`: brand gradient, nav/footer, code block styling, Quicksand font; bundled by Hugo assets pipeline.

## Content Workflow

- New post (file): `hugo new posts/my-post.md`; Page Bundle: `hugo new posts/my-post/index.md` then add images alongside `index.md` and reference `./image.png`.
- Recommended front matter:
  ```yaml
  ---
  author: ["Chris Achinga"]
  title: "Post Title"
  date: "2025-01-01"
  description: "One-line summary"
  tags: ["tag1", "tag2"]
  ShowToc: true
  TocOpen: true
  draft: true
  images: ["/images/og/cover.png"]   # for cards
  cover:
    image: "/images/headers/cover.png"
    alt: "Alt text"
    caption: "Caption"
  ---
  ```
- Publish by setting `draft: false`. Keep content types aligned with `params.mainsections` (`posts`, `papermod`).

## Configuration & Feature Toggles

- Edit `hugo.yaml`:
  - Menus: `languages.en.menu.main`.
  - Defaults: `params.defaultTheme`, `ShowShareButtons`, `ShowReadingTime`, `ShowPostNavLinks`, `ShowBreadCrumbs`, `ShowCodeCopyButtons`, `ShowToc`, `ShowPageNums`, `ShowAllPagesInArchive`, etc.
  - Home: `homeInfoParams` greeting uses `{{< articleCount >}}`; toggle `profileMode` for full-profile landing.
  - Search: `outputs.home` already includes JSON; create/adjust `content/search.md` for the search page.
  - Archives page: add `content/archives.md` with `layout: archives`.
  - Social icons: `params.socialIcons`.
  - SEO/social: `params.description`, `params.author`, `images` default array; `extend_head` uses `images/OG_IMAGE.png` if not overridden.
  - Full PaperMod switches live in `references/papermod-features.md` (covers, editPost links, comments, access keys, Fuse options, etc.).
- Comments: drop provider embed in `layouts/partials/comments.html`, then set `params.comments: true`.

## Styling & Layout

- CSS overrides: edit `assets/css/extended/custom.css` (palette, nav/footer, code blocks, font). Hugo rebuilds the bundle on serve/build.
- Header nav: adjust `layouts/partials/header.html` if menu/toggle behavior changes (theme button stores `pref-theme` in `localStorage`).
- Footer + a11y: `layouts/partials/footer.html` and `extend_footer.html` manage font scaling, contrast, theme persistence; keep the expected IDs (`font-smaller`, `font-reset`, `font-bigger`, `contrast-toggle`, `theme-toggle`) if you add controls.
- Images: `layouts/_default/_markup/render-image.html` emits lazy `<img>` with async decoding.
- Shortcodes: `articleCount` shows total published posts in main sections.

## SEO/Analytics

- `params.env: production` enables PaperMod enhanced SEO; `extend_head` adds meta/OG/Twitter and canonical tags. Override images/titles via front matter (`images`, `cover`, `description`).
- Analytics: Google Analytics supported via `params.analytics.google`; Cabin script already loaded in `footer.html`.

## Debugging & Checks

- Confirm Hugo Extended availability: `hugo version`.
- If builds fail, run `hugo --gc --minify` for validation; fix front matter (YAML, lowerCamel keys) and missing params.
- Avoid editing generated `public/` output; regenerate via `hugo --minify` when needed.

## References

- PaperMod feature toggles and examples: `references/papermod-features.md`.
- Formatting examples: `content/posts/formatting-posts-on-hugo.md`.
- Project quick start and commands: `README.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/achingachris) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
