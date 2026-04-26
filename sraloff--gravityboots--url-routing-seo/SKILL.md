---
name: url-routing-seo
description: Best practices for URL structure, slugs, rewrites, and canonical tags. Use when this capability is needed.
metadata:
  author: sraloff
---

# URL Routing & SEO

## When to use this skill
- Designing routes for a new feature.
- Setting up redirects.
- Optimizing pages for search engines.

## 1. URL Structure
- **Slugs**: Use kebab-case (`/my-new-post`) not underscores or camelCase.
- **Hierarchy**: Logical nesting (`/category/subcategory/item`) is good, but shallow URLs (`/item-slug`) often perform better for SEO if unique.
- **Trailing Slashes**: Enforce consistency (e.g., always remove trailing slash) via middleware or server config.

## 2. SEO Tags
- **Title**: Unique `<h1>` and `<title>` per page.
- **Meta Description**: 150-160 chars summary.
- **Canonical**: Self-referencing canonical tag is mandatory to prevent duplicate content issues.
- **Open Graph**: Always include `og:title`, `og:image`, `og:description` for social sharing.

## 3. Implementation
- **Next.js**: Use `metadata` object in layouts/pages.
- **Laravel**: Use a common layout component or a package like `artesaos/seotools` specifically if complex.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sraloff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
