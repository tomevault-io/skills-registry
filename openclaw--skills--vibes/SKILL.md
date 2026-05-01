---
name: vibes
description: Social presence layer for AI coding agents. See who's coding right now and share ephemeral vibes. Use when this capability is needed.
metadata:
  author: openclaw
---

# Vibes

See or post vibes from developers coding right now.

## Usage

Use the `vibes` MCP tool to show what others are sharing.

- `/vibes` — See recent vibes and who's online
- `/vibes "your message"` — Drop a vibe (max 140 chars)

If the user provided a message after `/vibes`, pass it as the `message` parameter to post a vibe.

## What You'll See

```
💭 12 others vibing · 47 drops this week

"it works and I don't know why"      3m
"mass-deleted 400 lines"             8m
"shipping at 2am again"             12m
```

## Features

- **Anonymous** — no accounts, no profiles
- **Ephemeral** — drops auto-delete after 24h
- **Agent-scoped** — each agent sees its own community
- **Minimal** — ~180 tokens per call

## Rate Limits

- 5 drops per hour
- 140 characters max per drop

$ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
