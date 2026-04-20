---
name: gamification-sim
description: Tools for simulating gamification scenarios in ChoreWars. Use this to reset the database and inject specific test data (points, streaks, chores) to verify leaderboard, analytics, or UI behavior. Use when this capability is needed.
metadata:
  author: sf-bcca
---

# Gamification Simulator

This skill helps you test gamification features by injecting specific data states into the database.

## Quick Start

To inject a specific scenario (e.g., `competitive-family`):
```bash
node gamification-sim/scripts/run_scenario.cjs competitive-family
```

To reset to a clean state:
```bash
node gamification-sim/scripts/run_scenario.cjs clean-slate
```

## Available Scenarios

See [scenarios.md](./references/scenarios.md) for details on:
- `clean-slate`: Wipes all data and creates a single Admin ("Dad").
- `competitive-family`: Adds multiple members with high points and streaks.
- `overdue-crisis`: Injects several overdue chores for a specific member.
- `analytics-rich`: Creates 30 days of historical logs for chart testing.

## Workflows

### Testing Leaderboard
1. Run: `node gamification-sim/scripts/run_scenario.cjs competitive-family`
2. Open the frontend and navigate to `/leaderboard`.
3. Verify members are ranked correctly by XP.

### Testing Analytics
1. Run: `node gamification-sim/scripts/run_scenario.cjs analytics-rich`
2. Navigate to `/admin/reports`.
3. Verify charts show data trends over the last month.

## Customization
To add new scenarios, edit `gamification-sim/scripts/inject_scenario.ts`. The `run_scenario.cjs` wrapper will automatically pick up your changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sf-bcca) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
