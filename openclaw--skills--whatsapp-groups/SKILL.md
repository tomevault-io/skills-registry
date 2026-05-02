---
name: whatsapp-groups
description: name: whatsapp-groups Use when this capability is needed.
metadata:
  author: openclaw
---
---
name: whatsapp-groups
description: Discover, list, and search WhatsApp groups from Baileys session data
---

# WhatsApp Groups Skill

Automatically discover WhatsApp groups the bot participates in by reading Baileys session cache files.

## Usage

```
exec({ cmd: "node <skill_dir>/scripts/groups.js COMMAND [ARGS]" })
```

## Commands

### List All Groups
```
exec({ cmd: "node <skill_dir>/scripts/groups.js list" })
```

### Search Group by Name
```
exec({ cmd: "node <skill_dir>/scripts/groups.js search \"Marketing\"" })
```

### Get Group ID by Name
```
exec({ cmd: "node <skill_dir>/scripts/groups.js get-id \"Support\"" })
```

### Sync Groups with Config
```
exec({ cmd: "node <skill_dir>/scripts/groups.js sync" })
```

## Output

Commands return JSON with group information:

```json
{
  "total": 3,
  "groups": [
    {
      "id": "5551XXXXXXXX-TIMESTAMP@g.us",
      "name": "Team Chat",
      "isActive": true
    }
  ]
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
