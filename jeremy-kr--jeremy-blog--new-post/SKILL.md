---
name: new-post
description: This skill should be used when the user asks to "create a new blog post", "write a new post", "새 포스트 생성", or "새 글 작성". Creates a new MDX blog post with proper frontmatter and structure. Use when this capability is needed.
metadata:
  author: jeremy-kr
---

# New Blog Post Creation

## Process

1. Accept a slug argument (e.g., `/new-post my-awesome-post`)
   - If no slug provided, ask the user for one
   - Validate: lowercase letters, numbers, hyphens only
   - Check `content/blog/<slug>.mdx` does not already exist

2. Ask the user for required metadata:
   - **title**: Post title in Korean
   - **description**: 1-2 sentence summary
   - **tags**: Array of relevant tags (check existing posts for consistency)

3. Create `content/blog/<slug>.mdx` with frontmatter:
   ```yaml
   ---
   title: '<user provided>'
   description: '<user provided>'
   date: '<today YYYY-MM-DD>'
   tags: [<user provided>]
   ---
   ```

4. Add a starter outline based on the title/description

5. Verify the post renders by checking that `getAllPosts()` in `src/lib/posts.ts` would pick it up

## Constraints

- Frontmatter fields: title (string), description (string), date (YYYY-MM-DD), tags (string[])
- Slug format: `/^[a-z0-9]+(-[a-z0-9]+)*$/`
- All content should be in Korean unless the user specifies otherwise
- Code blocks must always have a language identifier

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremy-kr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
