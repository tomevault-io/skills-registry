---
name: tv-spelunk
description: Search codebase for a pattern and open top hits. Use when this capability is needed.
metadata:
  author: neversight
---

# TV Spelunk

## Workflow
- Run `rg -n "<pattern>" src` (include `docs` when needed).
- Open top 2-3 hits with `sed -n '1,240p' <file>`.
- Summarize findings.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
