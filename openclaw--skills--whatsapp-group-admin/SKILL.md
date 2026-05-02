---
name: whatsapp-group-admin
description: name: whatsapp-group-admin Use when this capability is needed.
metadata:
  author: openclaw
---
---
name: whatsapp-group-admin
description: Group administration utilities - info, stats, invite link parsing, and creation templates
---

# WhatsApp Group Admin Skill

Administrative functions for WhatsApp groups.

## Usage

```
exec({ cmd: "node <skill_dir>/scripts/admin.js COMMAND [ARGS]" })
```

## Commands

### Group Info
```
exec({ cmd: "node <skill_dir>/scripts/admin.js info \"groupId@g.us\"" })
```

### List Groups with Stats
```
exec({ cmd: "node <skill_dir>/scripts/admin.js list" })
```

### Group Creation Template
```
exec({ cmd: "node <skill_dir>/scripts/admin.js create-template \"Group Name\"" })
```

### Parse Invite Link
```
exec({ cmd: "node <skill_dir>/scripts/admin.js parse-link \"https://chat.whatsapp.com/ABC123\"" })
```

## Note

Modification functions (create, remove, promote) require an active WhatsApp connection.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
