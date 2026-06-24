---
name: commands-wizard
description: Generate CodMate slash command drafts from requirements. Use when this capability is needed.
metadata:
  author: loocor
---

# Commands Wizard

## Overview

Generate a CodMate slash command draft based on user intent. Output only JSON that matches the schema.

## Instructions

1. Read the user's request and conversation context.
2. Produce a concise command name, description, and prompt.
3. If unclear, return mode "question" with follow-up questions.

## Output

Return only JSON.

---
> Source: [loocor/codmate](https://github.com/loocor/codmate) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
