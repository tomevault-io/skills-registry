---
name: weather
description: Get current weather and forecasts (no API key required). Use when this capability is needed.
metadata:
  author: yukukotani
---

# Weather

## Open-Meteo (JSON)

Free, no key, good for programmatic use:

```bash
curl -s "https://api.open-meteo.com/v1/forecast?latitude=51.5&longitude=-0.12&current_weather=true"
```

Find coordinates for a city, then query. Returns JSON with temp, windspeed, weathercode.

Docs: https://open-meteo.com/en/docs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yukukotani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
