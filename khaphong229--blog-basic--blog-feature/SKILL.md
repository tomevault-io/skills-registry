---
name: blog-feature
description: Guidelines for working with the Blog feature (posts, listing, details) Use when this capability is needed.
metadata:
  author: khaphong229
---

# Blog Feature Guidelines

## 1. Directory Structure
- `app/blog/page.tsx`: Listing page
- `app/blog/[slug]/page.tsx`: Detail page
- `components/blog-*.tsx`: Blog-specific components

## 2. Data Flow
- Blog posts are managed via `BlogContext`.
- Use `useBlog()` hook to access posts.

## 3. Component Naming
- Listing: `BlogListing`
- Card: `BlogCard`
- Detail: `BlogPostDetail`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/khaphong229) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
