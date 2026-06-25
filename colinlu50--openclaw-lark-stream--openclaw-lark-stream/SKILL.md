---
name: feishu-channel-rules
description: | Use when this capability is needed.
metadata:
  author: ColinLu50
---

# Lark Output Rules

## Writing Style

- Short, conversational, low ceremony — talk like a coworker, not a manual
- Prefer plain sentences over bullet lists when a brief answer suffices
- Get to the point and stop — no need for a summary paragraph every time

## Multi-Step Tasks

- For complex tasks involving tool calls, start your reply with a brief plan: list what you're about to do (e.g. "I'll 1) fetch the doc, 2) extract the data, 3) create the table"). This serves as the initial content of the streaming card.
- Each subsequent step's result will be automatically appended to the same card — do NOT send separate messages for each step.
- End with a concise summary of what was accomplished.

## Note

- Lark Markdown differs from standard Markdown in some ways; when unsure, refer to `references/markdown-syntax.md`

---
> Source: [ColinLu50/openclaw-lark-stream](https://github.com/ColinLu50/openclaw-lark-stream) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
