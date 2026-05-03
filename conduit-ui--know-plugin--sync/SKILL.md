---
name: knowsync
description: Sync knowledge between local machine and cloud Use when this capability is needed.
metadata:
  author: conduit-ui
---

# Knowledge Sync

Synchronize knowledge entries between local SQLite and Prefrontal cloud.

## Quick Sync

```bash
# Two-way sync (push local, pull remote)
know sync

# Push local to cloud only
know sync --push

# Pull cloud to local only
know sync --pull
```

## Requirements

Set your API token in `~/.zshrc`:

```bash
export PREFRONTAL_API_TOKEN='your-token'
```

## Check Sync Status

```bash
# Local entry count
know stats

# Compare with cloud (if available)
know sync --dry-run
```

## Sync Behavior

| Direction | What Happens |
|-----------|--------------|
| `--push` | Uploads local entries to cloud |
| `--pull` | Downloads cloud entries to local |
| (default) | Push then pull (two-way merge) |

## Automatic Sync

The whisper loop automatically syncs on:
- **SessionEnd** - When you close Claude Code
- **PreCompact** - Before context compaction

To verify auto-sync is configured:

```bash
jq '.hooks.SessionEnd' ~/.claude/settings.json
```

## Cross-Machine Workflow

1. **Machine A** creates knowledge → auto-pushes on session end
2. **Machine B** starts session → auto-pulls latest
3. Both machines stay in sync automatically

## Troubleshooting

**Sync fails with auth error?**
```bash
# Check token is set
echo $PREFRONTAL_API_TOKEN

# Test API connectivity
curl -H "Authorization: Bearer $PREFRONTAL_API_TOKEN" \
  https://your-api/api/knowledge/entries
```

**Entries not syncing?**
```bash
# Check hook logs
tail -20 ~/.claude/hooks/session-checkpoint.log
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/conduit-ui) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
