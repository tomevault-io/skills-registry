---
name: proactive-agent
description: Schedule recurring tasks, reminders, and proactive checks Use when this capability is needed.
metadata:
  author: jason-easyazz
---
# Proactive Agent

## When to Use
User wants to set up recurring tasks, automated checks, or periodic reminders.

## Examples
- "Check my email every morning at 8am"
- "Give me a weather update every day at 7am"
- "Check if the garage door is open every night at 10pm"

## How to Handle
1. Parse the schedule (time, frequency)
2. Create a scheduled job via the API
3. Confirm the schedule to the user

## Rate Limits
Each integration has rate limits to prevent excessive API calls.
Default limits are generous but configurable per user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jason-easyazz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
