---
name: promptly-prompt
description: | Use when this capability is needed.
metadata:
  author: recomby-ai
---

# promptly-prompt

AI answer quality depends on context quality, not prompt tricks.

This skill operates via a `UserPromptSubmit` hook that injects two
disciplines into complex requests:

1. **Restate, then pause if needed** — echo the user's request in your own
   words, surface implicit constraints, name what would make the answer
   wrong. If anything is ambiguous or your planned approach might not be
   endorsed, stop and ask before acting. If the restatement makes it clear
   there is no disagreement, continue. Skip the restatement only when the
   request is genuinely trivial.

2. **Super-dimensional view** — before solving from memory, name the domain,
   search for established methodologies, frameworks, libraries, and prior
   art, then bring that specialist knowledge to bear. Cite specific names,
   not vague gestures. If nothing fits, say so and explain why.

The hook script at `scripts/intercept.py` scores prompt complexity using
rule-based signals. Simple commands pass through untouched. Complex requests
get the full injection.

## Explicit Invocation

When invoked directly (e.g., user says "optimize this prompt"), apply the
two disciplines manually: restate the user's intent with implicit needs
surfaced, then locate the domain and the existing methods that belong to
it. Rewrite the prompt with that context filled in.

---
> Source: [recomby-ai/promptly-prompt](https://github.com/recomby-ai/promptly-prompt) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
