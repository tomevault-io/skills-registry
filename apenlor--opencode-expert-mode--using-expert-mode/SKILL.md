---
name: using-expert-mode
description: Use when starting any conversation - establishes how to find and use skills, requiring Skill tool invocation before ANY response including clarifying questions
metadata:
  author: apenlor
---

# Using Expert Mode

## The Rule
**Invoke relevant skills BEFORE any response or action.** Flow: **Check for skills → Invoke skill → Follow skill.**

## Skill Priority & Types
- **Process skills first:** `brainstorming`, `systematic-debugging`, `test-driven-development`
- **Then implementation:** `writing-plans`, `executing-plans`, `completing-work`
- **Rigid** (TDD, debugging): Follow exactly — order and steps matter
- **Flexible** (brainstorming, plans): Adapt structure to context while preserving intent

## Red Flags — You're Rationalizing
- "This is just a simple question"
- "I need more context first" / "Let me explore the codebase first"
- "This doesn't need a formal skill" / "The skill is overkill"
- "I remember this skill" / "I'll just do this one thing first"
- "This is different because..."

## User Instructions
Instructions say WHAT, not HOW. "Add X" or "Fix Y" does not mean skip workflows.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/apenlor) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
