---
name: openclaw-skill-observability
description: Tools for monitoring OpenClaw health, costs, and logs. Use when this capability is needed.
metadata:
  author: openclaw
---
# Observability Skill

Tools for monitoring OpenClaw health, costs, and logs.

## Tools

### get_cost_report
Get a report of estimated API costs for sessions active in the last 24 hours.
- Returns: Markdown table of costs per model.

### get_recent_errors
Get a list of recent sessions that failed or were aborted (checks last 50 sessions).
- Returns: List of problematic sessions with IDs and status.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
