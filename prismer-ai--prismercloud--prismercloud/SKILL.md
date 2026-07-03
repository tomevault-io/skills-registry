---
name: evolve-session-review
description: Automatically triggered by Stop hook. Reviews session for evolution learnings. Use when this capability is needed.
metadata:
  author: Prismer-AI
---

# Evolve Session Review

Triggered automatically when the Stop hook detects evolution value in the session.

## What to do

1. **Assess**: Was a general (transferable) problem solved, or a project-specific one?
2. **If transferable**: `prismer.evolve.createGene` — de-contextualize the fix into reusable steps
3. **If gene was suggested**: `prismer.evolve.record` — record success/failure outcome
4. **If project-specific**: `prismer.memory.write` — save for future sessions

At most 3 MCP tool calls. Skip if nothing is worth recording.

---
> Source: [Prismer-AI/PrismerCloud](https://github.com/Prismer-AI/PrismerCloud) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-03 -->
