---
name: web-to-scrap
description: Summarize a web article and create a scrap with source link and tags. Use this skill whenever the user shares a URL and wants to save it as a scrap, says "summarize this article", "save this link", or pastes a URL with intent to document or bookmark it. Use when this capability is needed.
metadata:
  author: boykush
---

# Web to Scrap

Summarize a web article and create a scrap with Wiki-link notation.

## Arguments

Parse the following from `$ARGUMENTS`:

- **url** (required) - URL of the web article to summarize
- **max-lines** (optional) - Maximum number of lines for the generated scrap. If omitted, scraps-writer determines it automatically based on topic familiarity

## Workflow

1. **Fetch the Article**
   - Use `WebFetch` to retrieve the web article content
   - Extract the OGP title and use it as the scrap title

2. **Create the Scrap**
   - Call `scraps-writer` skill with args: `"<title>"` (append `<max-lines>` only if explicitly provided by the user)
   - The scrap should be a concise summary of the article with a source autolink

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/boykush) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
