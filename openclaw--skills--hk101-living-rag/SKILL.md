---
name: hk101-living-rag
description: Simple RAG over local text/markdown. Use when this capability is needed.
metadata:
  author: openclaw
---
# claw-rag
Simple RAG over local text/markdown.

## Inputs
- query (string): question to answer.
- docsPath (string, optional): folder of docs (default ./docs relative to CWD).
- k (number, optional): number of top matches (default 3).

## Output
- answer: synthesized answer from matches.
- matches: [{path, score, snippet}...]

Requires: OPENAI_API_KEY in env.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
