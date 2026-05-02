---
name: notify
description: Send a push notification to your phone/devices via ntfy when a task completes. Use this whenever Claude finishes a long-running command, build, deployment, or background task and the user might want to be notified. Also use when the user says things like "let me know when done", "ping me", or "notify me". Use when this capability is needed.
metadata:
  author: claha
---

# Skill: Send Notification via ntfy

## Goal

Send a notification to your phone/devices when a long-running task completes.

## Setup Check

Verify ntfy is available before use:

```bash
nix-shell -p ntfy-sh --run "ntfy --version"
```

Default topic: `agent`. Server: `ntfy.hallstrom.duckdns.org`

## Basic Notification

```bash
nix-shell -p ntfy-sh --run "ntfy publish ntfy.hallstrom.duckdns.org/agent 'Task completed'"
```

## After Long-Running Command (Success + Failure)

Wrap any command to notify on both outcomes:

```bash
nix-shell -p ntfy-sh --run "ntfy publish ntfy.hallstrom.duckdns.org/agent 'Task succeeded'" || \
nix-shell -p ntfy-sh --run "ntfy publish --tags warning --priority high ntfy.hallstrom.duckdns.org/agent 'Task FAILED'"
```

## Title, Emoji Tags & Priority

Add a title, emojis, and priority level to make notifications more useful:

```bash
nix-shell -p ntfy-sh --run "ntfy publish \
  --title 'NixOS Deploy' \
  --tags white_check_mark,rocket \
  --priority high \
  ntfy.hallstrom.duckdns.org/agent 'Switch complete'"
```

**Tags** work two ways: if a tag matches an emoji shortcode (e.g. `warning`,
`skull`, `rocket`), it's prepended as an emoji to the notification. Other tags
appear as labels below the message. Comma-separate multiple tags.

**Priority levels:** `min` / `low` / `default` / `high` / `urgent` — controls
vibration and sound on your device.

**Useful emoji shortcodes for task notifications:**

- `white_check_mark` ✅ — success
- `warning` ⚠️ — failure or caution
- `rotating_light` 🚨 — urgent / critical
- `rocket` 🚀 — deploy / launch
- `stopwatch` ⏱️ — timing info
- `computer` 💻 — generic script/task

Full list: <https://docs.ntfy.sh/emojis/>

## Rich Markdown Notification

```bash
nix-shell -p ntfy-sh --run 'ntfy publish --markdown \
  --title "Deploy Complete" \
  --tags rocket \
  ntfy.hallstrom.duckdns.org/agent "# Success
**Duration:** 5 minutes
**Result:** All systems go"'
```

Use single quotes around the message to prevent bash from interpreting
special characters. With `--markdown`, use double quotes and escape
internally, or use a heredoc.

## Supported Markdown

**Bold**, *italic*, `# headings`, `- lists`, `` `code` ``, `[links](url)`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/claha) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
