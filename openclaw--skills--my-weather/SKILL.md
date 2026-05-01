---
name: my-weather
description: Get current weather using wttr.in (no API key required). Use when this capability is needed.
metadata:
  author: openclaw
---
# My Weather

Get current weather using wttr.in (no API key required).

## Example

```bash
curl -s "wttr.in/78023?format=3"
# Output: San Antonio: ⛅️ +28°C
```

## Location

Use city name, airport code, or zip code:

```bash
curl -s "wttr.in/London?format=3"
curl -s "wttr.in/JFK?format=3"
curl -s "wttr.in/78023?format=3"
```

## Format options

- `?format=3` - Compact one-liner
- `?format=%l:+%c+%t+%h+%w` - Custom format
- `?T` - Full forecast
- `?0` - Current only
- `?1` - Today only

## Units

- `?m` - Metric
- `?u` - USCS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
