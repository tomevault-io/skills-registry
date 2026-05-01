---
name: agent-defibrillator
description: Watchdog that monitors your AI agent gateway and restarts it when it crashes. Triggers on "install defibrillator", "agent watchdog", "gateway monitor", "auto-restart agent", or "keep agent alive". macOS launchd service with optional Discord notifications. Use when this capability is needed.
metadata:
  author: openclaw
---

# Agent Defibrillator

Watchdog service that monitors your AI agent gateway and restarts it when it crashes.

## What It Does

- Checks gateway health every 10 minutes
- Detects crashes and stale processes
- Auto-restarts with cooldown protection
- Optional Discord notifications on restart
- Detects version mismatches after updates

## Install

**Recommended (review code first):**
```bash
git clone https://github.com/hazy2go/agent-defibrillator.git
cd agent-defibrillator
./install.sh
```

## Verify

```bash
launchctl list | grep defib
```

## Configure

Edit `~/.openclaw/scripts/defibrillator.sh`:

| Variable | Default | Description |
|----------|---------|-------------|
| `DEFIB_GATEWAY_LABEL` | `ai.openclaw.gateway` | launchd service label |
| `DEFIB_RETRY_DELAY` | `10` | Seconds between retries |
| `DEFIB_MAX_RETRIES` | `3` | Retries before restart |
| `DEFIB_COOLDOWN` | `300` | Seconds between restarts |
| `DISCORD_CHANNEL` | (empty) | Your channel ID for notifications |

## Commands

```bash
# View logs
tail -f ~/.openclaw/logs/defibrillator.log

# Manual check
~/.openclaw/scripts/defibrillator.sh

# Stop watchdog
launchctl bootout gui/$(id -u)/com.openclaw.defibrillator

# Restart watchdog
launchctl kickstart -k gui/$(id -u)/com.openclaw.defibrillator
```

## Uninstall

```bash
launchctl bootout gui/$(id -u)/com.openclaw.defibrillator
rm ~/Library/LaunchAgents/com.openclaw.defibrillator.plist
rm ~/.openclaw/scripts/defibrillator.sh
```

## Requirements

- macOS (uses launchd)
- AI agent running via launchd (OpenClaw, etc.)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
