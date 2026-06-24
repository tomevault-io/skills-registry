---
name: gemini-review
description: Get a second opinion from Gemini on your current code changes. Analyzes staged/unstaged diffs and returns prioritized findings. Use when user asks to "review with Gemini", "Gemini code review", or "ask Gemini to check my code". Use when this capability is needed.
metadata:
  author: Lykhoyda
---

# Gemini Code Review

Review current code changes by delegating to the `gemini-reviewer` agent.

## Instructions

1. Gather the diff to review:
   - Run `git diff` to get unstaged changes
   - Run `git diff --cached` to get staged changes
   - Combine both into a single diff

2. If the diff is empty, inform the user there are no changes to review.

3. Launch the `gemini-reviewer` agent with the diff content. The agent handles the Gemini prompt structure and output formatting.

---
> Source: [Lykhoyda/ask-llm](https://github.com/Lykhoyda/ask-llm) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
