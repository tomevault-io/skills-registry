---
name: subwayskill
description: > Use when this capability is needed.
metadata:
  author: openclaw
---

# NYC Subway Departure Times

Run the `subwayskill` CLI to answer questions about NYC subway times.

## Commands

```bash
# All default stations (Clinton-Washington C, Bergen 2/3, 7 Av Q, Atlantic Av 4/5)
subwayskill

# Specific line and station (fuzzy match — lowercase, partial names work)
subwayskill C clinton-washington
subwayskill 2 bergen

# Filter direction: N (northbound/Manhattan) or S (southbound/Brooklyn)
subwayskill 2 bergen -d N

# Future time — automatically falls back to static schedule
subwayskill 2 bergen -t 19:00

# JSON output for structured data
subwayskill --json
subwayskill C clinton-washington --json

# Control time window (minutes)
subwayskill 2 bergen -w 60
```

## Interpreting output

- **realtime**: live data from MTA GTFS-RT feeds, shows relative times ("3 min") and absolute times
- **scheduled**: static GTFS schedule fallback, shows absolute times only — used when the requested time is beyond realtime coverage
- Directions show destination labels when available (e.g., "Manhattan-bound", "Coney Island-bound")

## Tips

- Station names are fuzzy-matched: "clinton" finds "Clinton-Washington Avs", "atlantic" finds "Atlantic Av-Barclays Ctr"
- If the user doesn't specify a station, run with no args to show all defaults
- If the user asks about a future time (e.g., "when's the 2 train at Bergen at 7pm?"), use `-t HH:MM`
- Use `--json` when you need to do further processing or comparisons
- An unknown station returns suggestions — relay those to the user

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
