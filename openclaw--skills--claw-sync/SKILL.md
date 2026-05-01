---
name: claw-sync
description: List all available backup versions Use when this capability is needed.
metadata:
  author: openclaw
---

# Claw Sync

Secure, versioned sync for OpenClaw memory and workspace.

## Commands

### /sync
Push your memory and skills to the remote repository.
```
/sync              → Push and create versioned backup
/sync --dry-run    → Preview what would be synced
```

### /restore
Restore memory and skills from the remote repository.
```
/restore                        → Restore latest version
/restore latest                 → Same as above
/restore backup-20260202-1430   → Restore specific version
/restore latest --force         → Skip confirmation
```

### /sync-status
Show sync configuration and local backup info.
```
/sync-status
```

### /sync-list
List all available backup versions.
```
/sync-list
```

---

## What Gets Synced

| File | Description |
|------|-------------|
| `MEMORY.md` | Long-term memory |
| `USER.md` | User profile |
| `SOUL.md` | Agent persona |
| `IDENTITY.md` | Agent identity |
| `TOOLS.md` | Tool configs |
| `AGENTS.md` | Workspace rules |
| `memory/*.md` | Daily logs |
| `skills/*` | Custom skills |

## NOT Synced (security)

- `openclaw.json` - Contains API keys
- `.env` - Contains secrets

## Setup Required

Create `~/.openclaw/.backup.env`:
```
BACKUP_REPO=https://github.com/username/your-repo
BACKUP_TOKEN=ghp_your_token
```

## Features

- 🏷️ **Versioned** - Each sync creates a restorable version
- 💾 **Disaster Recovery** - Local backup before every restore
- 🔒 **Secure** - No config files synced, token sanitization
- 🖥️ **Cross-platform** - Windows, Mac, Linux

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
