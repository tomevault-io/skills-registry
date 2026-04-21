---
name: new-blog
description: Create a new blog post for the Docusaurus tech blog with proper frontmatter, MDX structure, and images directory Use when this capability is needed.
metadata:
  author: ngtanthanh-qc
---

# Create a New Blog Post

Create a new blog post in the `/blog/` directory following the project's established conventions.

## Directory Structure

Create a folder-based blog post:
```
blog/YYYY-MM-DD-slug-name/
├── index.mdx
└── (optional images)
```

Use today's date: `$ARGUMENTS[0]` as the post title.

## Frontmatter Template

```mdx
---
title: <Title from arguments>
authors: [kaizen]
slug: <category/kebab-case-title>
tags: [<relevant tags from $ARGUMENTS[1] or inferred>]
---
```

## Content Guidelines

1. Start with a `## Introduction` section explaining the topic
2. Use clear headings (`##`, `###`) to organize content
3. Include code blocks with language identifiers (```python, ```bash, etc.)
4. Use Mermaid diagrams (```` ```mermaid ````) for architecture/flow visualizations when appropriate
5. Use KaTeX math expressions when needed: `$inline$` or `$$block$$`
6. Add `{/* truncate */}` after the introduction paragraph to control the blog list preview
7. Use MDX features (JSX components, imports) when it enhances the content
8. End with a summary or conclusion section

## Available MDX Features

- Mermaid diagrams (theme `@docusaurus/theme-mermaid` is configured)
- Live code blocks (`@docusaurus/theme-live-codeblock`)
- KaTeX math (via `remark-math` and `rehype-katex`)
- Image zoom (via `docusaurus-plugin-image-zoom`)
- Admonitions: `:::tip`, `:::note`, `:::warning`, `:::danger`, `:::info`

## Author

Always use `authors: [kaizen]` - this maps to the blog author defined in `blog/authors.yml`.

After creating the blog post, confirm the file was created and suggest running `npm start` to preview.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ngtanthanh-qc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
