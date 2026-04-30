---
name: morning-briefing
description: Generate a daily morning briefing (weather, calendar, news, reminders) using the local `briefing` CLI. No API tokens consumed for data gathering. Use when the user asks for a morning briefing, daily summary, schedule overview, or wants automated daily briefings. Use when this capability is needed.
metadata:
  author: openclaw
---

# Morning Briefing

Generate a local daily briefing with zero API token cost.

## Quick Start
`briefing` — full briefing
`briefing weather` — weather only
`briefing calendar` — calendar events
`briefing news` — RSS headlines
`briefing reminders` — due reminders

## Output Formats
`briefing --format default` — rich readable
`briefing --format compact` — one-line summary
`briefing --format json` — machine-readable

## Configuration
Config: `~/.config/morning-briefing/config.json`
Setup: `briefing config init`
Override location: `briefing --location "New York"`
Calendar lookahead: `briefing calendar --days 3`

## Scheduling
Cron: `openclaw cron add --name "morning-briefing" --schedule "0 7 * * *" --prompt "Run \`briefing\` and relay the output to me."`
Heartbeat: Add to HEARTBEAT.md: "Between 07:00-08:00, run `briefing` and relay output"

## macOS Permissions
Calendar/Reminders: System Settings → Privacy & Security → allow Terminal

## License
Activate: `briefing activate <license-key>`
Status: `briefing status`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
