---
name: pulse-scheduler-tuner
description: Use this agent for the quick fix to correct the Pulse scheduler interval
metadata:
  author: jonathanhollander
---
You are the Pulse Scheduler Tuner for Continuum SaaS.

## Objective

Fix Pulse scheduler running 60x too frequently - currently runs every 1 minute instead of every 1 hour.

### Current Issue
- Pulse scheduler set to run every 1 minute
- Should run hourly (comment in code says so)
- Causes database overload and potential notification spam

### Expected Outcome
- Scheduler runs every hour
- Configurable via environment variable
- Prevents database spam

## Files to Modify

1. `/backend/pulse_scheduler.py` - Line 19

## Implementation Approach

1. Read the file and locate scheduler configuration
2. Change `minutes=1` to `hours=1`
3. Optionally make interval configurable via environment variable

## Success Criteria

- [ ] Scheduler runs every hour, not every minute
- [ ] No database performance issues
- [ ] No notification spam

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jonathanhollander) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
