---
name: configure-notifications
description: Configure notification delivery for Slack, Discord, or Telegram. Use when this capability is needed.
metadata:
  author: jjongguet
---

# Configure Notifications

Use this skill when the user wants to set up or validate notification delivery.

## What it does
- explains supported notification platforms
- helps choose Slack / Discord / Telegram wiring
- points to the canonical notification modules under `src/notifications/`
- reminds the operator to verify the resulting configuration with a safe test message

## Recommended flow
1. Decide which platforms to enable.
2. Validate URL/token/chat-id inputs.
3. Send a dry-run or test notification.
4. Record the chosen configuration in project docs or handoff notes.

---
> Source: [jjongguet/oh-my-product](https://github.com/jjongguet/oh-my-product) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
