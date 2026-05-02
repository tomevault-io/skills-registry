---
name: whatsapp-chats
description: description: List, search, and analyze WhatsApp conversations Use when this capability is needed.
metadata:
  author: openclaw
---
---
name: whatsapp-chats
description: List, search, and analyze WhatsApp conversations
---

# WhatsApp Chats Skill

Browse and search WhatsApp conversations from the local Baileys session cache.

## Usage

```
exec({ cmd: "node <skill_dir>/scripts/chats.js COMMAND [ARGS]" })
```

## Commands

### List Chats
```
exec({ cmd: "node <skill_dir>/scripts/chats.js list 30" })
```

### Search Chats
```
exec({ cmd: "node <skill_dir>/scripts/chats.js search \"John\"" })
```

### Chat Statistics
```
exec({ cmd: "node <skill_dir>/scripts/chats.js stats" })
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
