---
name: perplexity-research
description: Use Perplexity Search API for fast real-time web research without full browser automation. Use when this capability is needed.
metadata:
  author: ogodevo
---

# Perplexity Research Skill

Use this skill when the user asks for current web information and browser interaction is not required.

## Preferred Tool Order
1. Use `perplexity_search` first for retrieval speed and source ranking.
2. Use browser tools only if the user needs interaction, screenshots, or page-level actions.

## Query Strategy
- Keep query concise and intent-driven.
- For regional topics, set `country` (ISO code like `US`, `GB`).
- For broad research, use `queries` (multi-query) and synthesize across result sets.

## Output Rules
- Return the top sources with title, URL, and snippet.
- Call out uncertainty when sources conflict.
- For high-stakes topics, explicitly state that results should be independently verified.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ogodevo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
