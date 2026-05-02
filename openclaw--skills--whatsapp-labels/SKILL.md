---
name: whatsapp-labels
description: name: whatsapp-labels Use when this capability is needed.
metadata:
  author: openclaw
---
---
name: whatsapp-labels
description: List and search WhatsApp Business labels/tags
---

# WhatsApp Labels Skill

Manage and search WhatsApp Business labels (tags) from the local session cache.

## Usage

```
exec({ cmd: "node <skill_dir>/scripts/labels.js COMMAND [ARGS]" })
```

## Commands

### List All Labels
```
exec({ cmd: "node <skill_dir>/scripts/labels.js list" })
```

### Find Chats by Label
```
exec({ cmd: "node <skill_dir>/scripts/labels.js chats \"VIP Client\"" })
```

## Note

Requires a WhatsApp Business account for labels to be available.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
