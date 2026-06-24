---
name: calendar-events
description: Create, view, and manage calendar events and appointments Use when this capability is needed.
metadata:
  author: jason-easyazz
---
# Calendar Events Skill

Create, view, and manage calendar events and appointments.

## Behavior

1. Parse the user's intent (create, view, update, cancel)
2. Extract event details: title, date/time, duration, location
3. For ambiguous times, ask for clarification
4. Call the appropriate API endpoint
5. Confirm with event details and time

## Response Style

Clear and time-aware. Always confirm the exact date/time of created events.
Use relative time references ("in 2 hours", "tomorrow at 3pm").

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jason-easyazz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
