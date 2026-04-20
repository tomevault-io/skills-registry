---
name: weather
description: Get current weather and short forecasts quickly using `wttr.in` (no API key required). Use when users ask for weather by city/region. Use when this capability is needed.
metadata:
  author: jl-grey-man
---

# Weather

Use this skill for quick weather lookups without API keys.

## Current weather

```bash
curl -s "wttr.in/San+Francisco?format=3"
```

## Compact format

```bash
curl -s "wttr.in/San+Francisco?format=%l:+%c+%t+%h+%w"
```

## Multi-day forecast

```bash
curl -s "wttr.in/San+Francisco?m"
```

## Usage guidance

- URL-encode spaces with `+`.
- Use `?m` for metric and `?u` for US units.
- For ambiguous place names, clarify state/country first.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jl-grey-man) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
