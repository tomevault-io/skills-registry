---
name: openclaw-update
description: Check for OpenClaw updates, perform updates, and manage version status. Provides automatic update checking, version comparison, and seamless updating of the OpenClaw agent platform. Use when this capability is needed.
metadata:
  author: openclaw
---

# OpenClaw Update Skill

A skill for checking and applying OpenClaw updates.

## Commands

### Check Status

Check the current OpenClaw version and available updates:

```bash
openclaw update status
openclaw update status --json
```

### Run Update

Update OpenClaw to the latest version:

```bash
openclaw update run
```

### View Config

View current update configuration:

```bash
openclaw update config
```

## Use Cases

- **Automatic update checks**: Configure cron jobs for periodic update checks
- **Version monitoring**: Monitor if a newer version is available
- **Safe updates**: Check before updating to ensure compatibility
- **Update history**: Track when updates were applied

## Environment Variables

- `OPENCLAW_UPDATE_CHECK_URL`: Override update check URL (optional)

## Example Workflow

```bash
# Check for updates (human-readable output)
openclaw update status

# Check for updates (JSON output for automation)
openclaw update status --json

# If update available, apply it
openclaw update run
```

## Notes

- Requires OpenClaw to be installed
- Supports semantic versioning for version comparison
- Provides detailed change logs when updating
- Supports rollback if needed (check documentation)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
