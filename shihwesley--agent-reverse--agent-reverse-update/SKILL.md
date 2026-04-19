---
name: agent-reverse-update
description: Check for AgentReverse skill updates and Claude Code changelog changes on session start. Runs automatically via SessionStart hook — compares installed versions against remote, applies safe migrations, and flags breaking changes. Use when user says /agent-reverse-update to manually trigger an update check. Use when this capability is needed.
metadata:
  author: shihwesley
---

# Agent Reverse Update

Checks for skill updates automatically on session start.

## How It Works

On session start, this skill checks your `agent-reverse.json` manifest against the remote repos to see if any pinned commits have newer versions available.

## Manual Check

To manually check and update:

1. **Check for updates:** Use `manifest_check_updates` MCP tool to see which skills have updates
2. **View outdated:** The tool returns `outdated` array with `id`, `pinnedCommit`, and `latestCommit`
3. **Update all:** Use `manifest_sync` MCP tool to reinstall all skills from latest commits

## Example Usage

```
# Check updates
Use manifest_check_updates tool

# Sync to latest
Use manifest_sync tool
```

## Suppressing Notifications

If you don't want session-start notifications, remove this skill from `.claude/commands/`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shihwesley) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
