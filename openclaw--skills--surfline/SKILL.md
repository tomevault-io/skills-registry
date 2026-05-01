---
name: surfline
description: Get surf forecasts and current conditions from Surfline public endpoints (no login). Use to look up Surfline spot IDs, fetch forecasts/conditions for specific spots, and summarize multiple favorite spots. Use when this capability is needed.
metadata:
  author: openclaw
---

# Surfline (public, no login)

This skill uses Surfline **public endpoints** (no account, no cookies).

## Quick start

1) Find a spot id:

```bash
python3 scripts/surfline_search.py "Cardiff Reef"
python3 scripts/surfline_search.py "D Street"
```

2) Get a report for a spot id (prints text + JSON by default):

```bash
python3 scripts/surfline_report.py <spotId>
# or only one format:
python3 scripts/surfline_report.py <spotId> --text
python3 scripts/surfline_report.py <spotId> --json
```

3) Favorites summary (multiple spots) (prints text + JSON by default):

Create `~/.config/surfline/favorites.json` (see `references/favorites.json.example`).

```bash
python3 scripts/surfline_favorites.py
```

## Notes

- Keep requests gentle: don’t hammer endpoints. Scripts include basic caching.
- Spot IDs are stable; store them once.
- If Surfline changes endpoints/fields, update `scripts/surfline_client.py`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
