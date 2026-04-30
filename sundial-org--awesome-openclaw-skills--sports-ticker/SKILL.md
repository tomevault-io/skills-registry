---
name: sports-ticker
description: Live sports alerts for Soccer, NFL, NBA, NHL, MLB, F1 and more. Real-time scoring with FREE ESPN API. Track any team from any major league worldwide. Use when this capability is needed.
metadata:
  author: sundial-org
---

# Sports Ticker

Track your favorite teams across **multiple sports** with **FREE live alerts**!

Supports: ⚽ Soccer • 🏈 NFL • 🏀 NBA • 🏒 NHL • ⚾ MLB • 🏎️ F1

## First Run (Onboarding)

When no `config.json` exists, running the setup script launches an interactive wizard:

```bash
python3 scripts/setup.py
```

**The wizard asks:**
1. 📺 **Which sports?** — Pick from Soccer, NFL, NBA, NHL, MLB, F1
2. 🏆 **Which teams?** — Choose from popular teams or search for any team
3. 🔔 **Alert style?** — Live scores, final only, or daily digest
4. ⏰ **Game-day reminders?** — Get a heads-up 30 mins before kick-off
5. 🌙 **Quiet hours?** — Pause alerts while you sleep

After setup, your `config.json` is ready and you can start tracking!

**Re-run setup anytime:**
```bash
python3 scripts/setup.py --force  # Overwrites existing config
```

## Quick Start

```bash
# First time? Just run setup!
python3 scripts/setup.py  # Interactive wizard

# Find team IDs (any sport)
python3 scripts/setup.py find "Lakers" basketball
python3 scripts/setup.py find "Chiefs" football
python3 scripts/setup.py find "Barcelona" soccer

# Test
python3 scripts/ticker.py
```

## Config Example

```json
{
  "teams": [
    {
      "name": "Barcelona",
      "emoji": "🔵🔴",
      "sport": "soccer",
      "espn_id": "83",
      "espn_leagues": ["esp.1", "uefa.champions"]
    },
    {
      "name": "Lakers",
      "emoji": "🏀💜💛",
      "sport": "basketball",
      "espn_id": "13",
      "espn_leagues": ["nba"]
    }
  ]
}
```

## Commands

```bash
# Ticker for all teams
python3 scripts/ticker.py

# Live monitor (for cron)
python3 scripts/live_monitor.py

# League scoreboard
python3 scripts/ticker.py league nba basketball
python3 scripts/ticker.py league nfl football
python3 scripts/ticker.py league eng.1 soccer

# ESPN direct
python3 scripts/espn.py leagues
python3 scripts/espn.py scoreboard nba basketball
python3 scripts/espn.py search "Chiefs" football
```

## Alert Types

- 🏟️ Game start (kick-off / tip-off)
- ⚽🏈🏀⚾ Scoring plays (goals, touchdowns, 3-pointers, home runs)
- 🟥 Red cards / Ejections
- ⏸️ Halftime / Period breaks
- 🏁 Final results (WIN/LOSS/DRAW)

## ESPN API (Free!)

No key needed. Covers all major sports and 50+ leagues worldwide.

**Supported Sports:**
- Soccer: Premier League, La Liga, Champions League, MLS, and 30+ more
- Football: NFL
- Basketball: NBA, WNBA, NCAA
- Hockey: NHL
- Baseball: MLB
- Racing: Formula 1

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
