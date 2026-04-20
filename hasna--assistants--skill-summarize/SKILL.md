---
name: summarize
description: Summarize content from URLs, documents, or text. Use when the user wants a quick summary of something. Use when this capability is needed.
metadata:
  author: hasna
---

## Instructions

Summarize the following content: $ARGUMENTS

### Steps

1. If the input is a URL, fetch the content using web_fetch
2. If the input is a file path, read the file
3. Otherwise, treat the input as text to summarize

### Output Format

Provide a summary with:
- **TL;DR**: One sentence summary
- **Key Points**: 3-5 bullet points of the main ideas
- **Notable Details**: Any important specifics worth highlighting

Keep the summary concise but comprehensive. Adapt the length based on the source content.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hasna) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
