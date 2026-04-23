---
name: repo-grounding
description: Ground answers in the repository by reading relevant files, configs, and code paths. Use when the user asks to modify/debug code, configuration, CI/CD, or wants repo-specific reasoning. Use when this capability is needed.
metadata:
  author: janjaszczak
---

# repo-grounding

## Activation gate (anti-noise)
Run only if the user asks for:
- Code change, refactor, fix, debug, performance work in THIS repo.
- Analysis of existing config/scripts.
- “Where is X implemented?” in this repo.

Skip if:
- Pure conceptual question (no repo action).
- The user wants a generic best-practice answer.

## Procedure
1. Identify likely files (paths mentioned, conventional locations).
2. Read the minimum subset (start with entrypoints/config).
3. Quote exact fragments when asserting repo facts.
4. If a change is requested: propose plan with file list and exact edits.

## Output
- Evidence-based: cite file paths + key excerpts + recommended edit plan.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/janjaszczak) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
