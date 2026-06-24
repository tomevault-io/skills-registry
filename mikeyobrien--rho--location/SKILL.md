---
name: location
description: Get device location via GPS or network. Use for location-aware actions, finding nearby places, or logging position. Use when this capability is needed.
metadata:
  author: mikeyobrien
---

# Location

## Get current location
```bash
termux-location                    # default (GPS if available)
termux-location -p gps             # GPS only (more accurate, slower)
termux-location -p network         # Network only (faster, less accurate)
termux-location -p passive         # Last known location (instant)
```

## Request single update (default)
```bash
termux-location -r once
```

## Continuous updates
```bash
termux-location -r updates         # keep updating until killed
```

## Output format
```json
{
  "latitude": 37.7749,
  "longitude": -122.4194,
  "altitude": 10.0,
  "accuracy": 20.0,
  "bearing": 0.0,
  "speed": 0.0,
  "provider": "gps"
}
```

**Note:** Requires location permission. GPS may take 10-30s for first fix.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeyobrien) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
