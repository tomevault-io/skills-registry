---
name: start-gap-finder
description: Find conceptual gaps in plan/spec documents. Use when user explicitly asks start-gap-finder. Use when this capability is needed.
metadata:
  author: ohadrubin
---

# Plan Gap Finder

Find conceptual gaps by running three steps in sequence. Each step is a separate skill invocation.

## Procedure

1. Invoke `gap-finder-1` skill on the document
2. Invoke `gap-finder-2` skill (uses Step 1 output from conversation)
3. Invoke `gap-finder-3` skill (uses Step 2 output from conversation)

**IMPORTANT:** Do NOT stop between steps. Output what each step requires, then immediately continue to the next skill. Run all three steps in a single turn.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ohadrubin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
