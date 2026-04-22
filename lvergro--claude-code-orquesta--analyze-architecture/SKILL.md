---
name: analyze-architecture
description: > Use when this capability is needed.
metadata:
  author: lvergro
---

# /analyze-architecture — Drift Detection

Read `.claude/models.yml` for model routing. This skill uses model: opus.

## context
1. Read `.claude/memory/architecture.md` — the source of truth
2. Explore current directory structure and file patterns

## analysis
1. Are there undocumented directories or modules?
2. Are files in forbidden locations (e.g., business logic in view layer)?
3. Are naming conventions respected?
4. Do database queries match documented patterns?
5. Are new features properly declared in architecture.md?

## output
- If consistent: "✅ Architecture consistent."
- If violations found: List each violation with suggested fix.

## constraints
- Do NOT modify any files during analysis. Report only.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lvergro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
