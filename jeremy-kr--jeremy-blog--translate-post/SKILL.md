---
name: translate-post
description: This skill should be used when the user asks to "translate a post", "translate to English", "영어로 번역", or "포스트 번역". Translates a Korean blog post to English while preserving MDX structure and code blocks. Use when this capability is needed.
metadata:
  author: jeremy-kr
---

# Blog Post Translation (Korean → English)

## Process

1. Read the specified MDX file (or ask which post to translate)

2. **Review the Korean version first**
   - Run a quick quality check (frontmatter, structure, code blocks)
   - Flag any issues in the Korean version before translating

3. **Translate the post**
   - Translate title, description, and body content to natural English
   - Keep all code blocks, frontmatter field names, and technical terms unchanged
   - Preserve MDX component usage and JSX syntax exactly
   - Maintain heading hierarchy and document structure
   - Adapt Korean-specific idioms to natural English equivalents

4. **Save the translated version**
   - Create `content/blog/<slug>.en.mdx` alongside the Korean version
   - Update frontmatter: keep same date and tags, translate title and description

5. **Post-translation review**
   - Verify MDX syntax is valid
   - Check that code blocks and inline code are untouched
   - Ensure natural English tone (not machine-translated feel)

## Translation Guidelines

- Use active voice where possible
- Keep sentences concise and clear
- Technical terms should follow common English conventions
- Don't translate brand names, library names, or API names
- Preserve original formatting (bold, italic, links)
- Code comments inside code blocks: translate to English

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremy-kr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
