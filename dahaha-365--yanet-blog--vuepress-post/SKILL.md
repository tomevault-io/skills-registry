---
name: vuepress-post
description: Create new blog posts for YaNet VuePress blog with proper frontmatter and structure Use when this capability is needed.
metadata:
  author: dahaha-365
---

# VuePress Blog Post Creation

This skill provides guidelines for creating new blog posts in the YaNet VuePress blog project.

## Frontmatter Requirements

Every blog post MUST include the following frontmatter at the top of the file:

```yaml
---
tags:
  - Tag1
  - Tag2
title: Article Title
copyright:
  creation: original
  author:
    name: YaNet
createTime: 2026/01/06 22:09:35
permalink: /blog/unique-id/
---
```

## Frontmatter Field Guidelines

- **tags**: Array of tags, 1-5 relevant tags recommended
- **title**: Descriptive title in Chinese (primary language is zh-CN)
- **copyright**: Always include creation type and author info
- **createTime**: Format as `YYYY/MM/DD HH:mm:ss`
- **permalink**: Must be unique, use `/blog/unique-id/` format where `unique-id` is kebab-case

## File Location

- Create new posts in `docs/blog/<category>/` directory
- Use category-specific directories when appropriate
- Filename should match permalink for consistency

## Content Guidelines

- **Language**: Write content in Chinese (zh-CN)
- **Internal Links**: Use permalinks for internal references: `[text](/blog/permalink-id/)`
- **External Links**: Include full URLs
- **Images**: Store in CDN or `docs/.vuepress/public/`, use descriptive alt text

## Code Examples

When including code in the post:

1. Use proper markdown code blocks with language specification:
   \`\`\`typescript
   // code here
   \`\`\`

2. For VuePress-specific code, refer to existing patterns in the codebase

## Testing

After creating a post:

1. Test with `pnpm docs:dev` to verify rendering
2. Check that all internal links work
3. Verify frontmatter is correctly parsed
4. Ensure the post appears in the blog list

## Common Mistakes to Avoid

- Missing required frontmatter fields
- Using duplicate permalinks
- Incorrect date format in createTime
- Forgetting to set author name to "YaNet"
- Using empty alt text for images
- Not testing before committing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dahaha-365) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
