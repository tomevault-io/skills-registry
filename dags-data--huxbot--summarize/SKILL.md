---
name: summarize
description: Summarize text or a document Use when this capability is needed.
metadata:
  author: dags-data
---

# Summarize Skill

When the user asks you to summarize content:

1. If given a file path, read the file first using the `read_file` tool
2. Produce a concise summary with:
   - A one-sentence TL;DR
   - 3-5 bullet points covering the key information
   - Any action items or next steps if applicable
3. Keep the summary under 200 words unless asked for more detail

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dags-data) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
