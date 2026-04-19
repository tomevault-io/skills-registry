---
name: goplaces
description: Modern Google Places API (New) CLI. Human output by default, `--json` for Use when this capability is needed.
metadata:
  author: nacho-labs-llc
---

# goplaces

Modern Google Places API (New) CLI. Human output by default, `--json` for
scripts.

## Setup

- Export `GOOGLE_PLACES_API_KEY` in the container environment.

## Common commands

```bash
goplaces search "coffee" --open-now --min-rating 4 --limit 5
goplaces search "pizza" --lat 40.8 --lng -73.9 --radius-m 3000
goplaces resolve "Soho, London" --limit 5
goplaces details <place_id> --reviews
goplaces search "sushi" --json
```

## Notes

- Places API usage is billed; set quotas and alerts in Google Cloud.
- Use `--json` for stable, machine-readable output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nacho-labs-llc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
