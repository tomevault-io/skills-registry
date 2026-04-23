---
name: context-packer
description: Build a compact, high-signal context brief (files, symbols, recent commits) instead of pasting large code blocks Use when this capability is needed.
metadata:
  author: macroman5
---

# Context Packer

## Purpose
Reduce token usage by summarizing only what’s needed for the task.

## Behavior
1. Produce a 10–20 line brief:
   - File map (key paths)
   - Key symbols/functions/classes
   - Last 3 relevant commits (subject only)
   - Pointers to exact files/lines if code is needed
2. Include ≤1 short code window only if critical.

## Guardrails
- Never paste large files; link paths/lines instead.
- Prefer bullets over prose.

## Integration
- `UserPromptSubmit` enrichment; before sub-agent calls.

## Example Prompt
> Pack context to implement auth middleware with minimal tokens.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/macroman5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
