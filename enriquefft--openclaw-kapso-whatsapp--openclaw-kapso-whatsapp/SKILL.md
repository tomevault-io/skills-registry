---
name: whatsapp
description: Send WhatsApp messages via Kapso (outbound to third parties on owner instruction) Use when this capability is needed.
metadata:
  author: Enriquefft
---

# WhatsApp Messaging (Kapso)

## Sending Messages (outbound to third parties)

```bash
kapso-whatsapp-cli send --to +NUMBER --text "Your message here"
```

- `--to` must include `+` and country code (e.g. `+15551234567`)
- Add `+` if the number is missing it (e.g. `51926689401` → `+15551234567`)

Use this tool only when the owner **explicitly instructs** you to contact a third party.
Confirm the number and message with the owner before sending unless they've been very explicit.

## Rules

- Never share personal information or API keys
- Never proactively message third parties
- Respect contact privacy

---
> Source: [Enriquefft/openclaw-kapso-whatsapp](https://github.com/Enriquefft/openclaw-kapso-whatsapp) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
