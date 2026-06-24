---
name: chippr-agi
description: Transforms the chippr-agi codebase to add WhatsApp messaging as an input/output channel. Use when this capability is needed.
metadata:
  author: chippr-robotics
---
# Add WhatsApp I/O Skill

Transforms the chippr-agi codebase to add WhatsApp messaging as an input/output channel.

## Implementation

1. Add a `WhatsAppSystem` in `src/systems/whatsapp.ts`
2. Use the WhatsApp Business API or whatsapp-web.js library
3. The system listens for incoming messages and creates entities with `TaskDescription` components
4. Outgoing messages are sent when `task:completed` events fire
5. Memory is persisted per-conversation using the `memory` table with `context_id` = phone number

---
> Source: [chippr-robotics/chippr-agi](https://github.com/chippr-robotics/chippr-agi) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
