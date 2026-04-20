---
name: yaml-frontmatter-content-separation
description: Guide for separating YAML frontmatter from content when processing Story or ADR files for AI prompts. Use this when working with file content extraction for AI processing. Use when this capability is needed.
metadata:
  author: markusheiliger
---

## YAML Frontmatter Content Separation

When processing Story or ADR files for AI prompts, ALWAYS separate metadata from content.

**DO:**
- Extract content by detecting and removing YAML frontmatter delimited by `---` markers
- Normalize line endings (`\r\n` to `\n`) before processing
- Trim leading whitespace after frontmatter; preserve trailing whitespace
- Return entire file as content if no valid frontmatter exists
- Handle malformed YAML (missing closing `---`) by treating entire file as content

**DON'T:**
- Include YAML frontmatter (state, score) when sending content to AI prompts
- Treat `---` inside markdown code blocks as frontmatter delimiters
- Modify metadata values during content extraction

**Rationale:** Models may misinterpret metadata values, produce inappropriate responses, or conflate scoring with content generation if frontmatter is included.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/markusheiliger) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
