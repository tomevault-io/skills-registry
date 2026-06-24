---
name: memory-query
description: Queries repo memory sources (MEMORY.md focus view + memory/{st,lt} engrams). Use when asked about prior decisions, conventions, or to locate a past design note. Use when this capability is needed.
metadata:
  author: p3ngu1nzz
---

# Skill: memory-query

## What this skill does

Standardizes how to answer questions like:

- "Have we decided X already?"
- "Where is the spec for Y?"
- "What was the last consensus on Z?"

Sources:

- `MEMORY.md` (high-signal focus view)
- `memory/catalog.json`
- `memory/st/**` (short-term engrams)
- `memory/lt/**` (long-term engrams)

## Procedure

1. Search `MEMORY.md` first for current focus and recent context.
2. If needed, search `memory/catalog.json` and then the engram directories.
3. When reporting results, include:
   - file path(s)
   - a short quote/snippet
   - why it is relevant

## Output format

Prefer a concise bullet list with citations to file paths.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/p3ngu1nzz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
