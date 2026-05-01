---
name: feishu-group-manager
description: Manage Feishu group chats (settings, names, metadata). Use when this capability is needed.
metadata:
  author: openclaw
---
# Feishu Group Manager

Manage Feishu group chats (settings, names, metadata).

## Tools

### Toggle Busy Status
Marks the group name with a prefix (e.g., `[⏳]`) to indicate the bot is busy processing a long task.

```bash
node skills/feishu-group-manager/toggle_busy.js --chat-id <chat_id> --mode <busy|idle>
```

### Update Settings
Update group name, description (announcement area), and permissions.

```bash
node skills/feishu-group-manager/update_settings.js --chat-id <chat_id> [options]
```

**Options:**
- `-n, --name <text>`: New Group Name
- `-d, --description <text>`: New Group Description
- `--edit-permission <all_members|only_owner>`: Who can edit group info
- `--at-all-permission <all_members|only_owner>`: Who can @All
- `--invite-permission <all_members|only_owner>`: Who can invite others

## Usage Protocol
See `MEMORY.md` -> "Busy Status Protocol".
- Trigger: Long-running tasks (>30s) in 1-on-1 control groups.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
