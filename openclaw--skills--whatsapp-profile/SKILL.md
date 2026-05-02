---
name: whatsapp-profile
description: name: whatsapp-profile Use when this capability is needed.
metadata:
  author: openclaw
---
---
name: whatsapp-profile
description: View bot profile info and lookup contact profiles
---

# WhatsApp Profile Skill

Access profile information for the connected bot and individual contacts.

## Usage

```
exec({ cmd: "node <skill_dir>/scripts/profile.js COMMAND [ARGS]" })
```

## Commands

### Bot Info
```
exec({ cmd: "node <skill_dir>/scripts/profile.js me" })
```

### Contact Profile
```
exec({ cmd: "node <skill_dir>/scripts/profile.js contact \"5511999999999\"" })
```

### List Named Contacts
```
exec({ cmd: "node <skill_dir>/scripts/profile.js list 50" })
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
