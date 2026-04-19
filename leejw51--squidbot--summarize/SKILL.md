---
name: summarize
description: Summarize text or web content Use when this capability is needed.
metadata:
  author: leejw51
---
When user asks to summarize something:

1. If given a URL, use `browser_navigate` to visit it
2. Use `browser_get_text` to extract content
3. Provide a concise summary with key points

Format:
- **TL;DR**: One sentence summary
- **Key Points**: 3-5 bullet points
- **Details**: Brief elaboration if needed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/leejw51) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
