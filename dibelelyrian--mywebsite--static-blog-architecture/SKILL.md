---
name: static-blog-architecture
description: Design scalable static blog structures with Markdown content, including category, tag, and slug routing. Use when this capability is needed.
metadata:
  author: dibelelyrian
---

Use this skill when building or refactoring the static blog architecture.

Guidelines:
- Keep content in Markdown under a clear content directory structure.
- Define stable slugs and route patterns for posts, categories, and tags.
- Prefer deterministic routing based on frontmatter and file paths.
- Keep the build static and predictable for Netlify deployments.
- Provide real category/tag archive routes (not just query params) and include them in sitemap/prerender.
- Align internal links and canonicals with the chosen trailing-slash style.
- After changes, always run `npm run build`, review the output, and fix any build errors before reporting completion.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dibelelyrian) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
