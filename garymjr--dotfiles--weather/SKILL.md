---
name: weather
description: Read the latest Colorado daily weather forecast by scraping https://kodythewxguy.com/colorado-daily-weather-forecast/ with a local Python script (non-API source). Use when user asks for Denver/Front Range Colorado forecast details, latest daily weather writeup, or forecast discussion. Use wttr.in commands as fallback for quick current conditions. Use when this capability is needed.
metadata:
  author: garymjr
---

# Weather Skill

Read Colorado daily forecast text from Kody the WX Guy, then summarize it for Gary.

## Primary Workflow (Kody Colorado Daily Forecast)

Run the scraper:

```bash
python3 ~/.config/zigbot/skills/weather/scripts/kody_colorado_forecast.py
```

Useful options:

```bash
# Machine-readable output
python3 ~/.config/zigbot/skills/weather/scripts/kody_colorado_forecast.py --json

# Include more paragraphs
python3 ~/.config/zigbot/skills/weather/scripts/kody_colorado_forecast.py --max-paragraphs 12

# Include sponsor/promotional section
python3 ~/.config/zigbot/skills/weather/scripts/kody_colorado_forecast.py --include-promotional
```

What the script extracts:

- Page publication timestamp (`datePublished`)
- Forecast date heading
- Main forecast headline
- Forecast paragraphs (trimmed by default)
- Embedded forecast image URLs

## Fallback Workflow (wttr.in Quick Conditions)

Use this when the site structure changes or you only need a quick temperature snapshot.

### Current Weather (Johnstown)

```bash
curl -s wttr.in/Johnstown+Colorado?0pq
```

### Current Weather (Denver)

```bash
curl -s wttr.in/Denver?0pq
```

### 3-Day Forecast (Johnstown)

```bash
curl -s wttr.in/Johnstown+CO?2pq
```

### Moon Phase

```bash
curl -s wttr.in/moon?lang=en
```

## Notes

- The scraper uses browser-like headers to avoid 403 responses.
- The source page is WordPress and can change markup, keep wttr fallback available.
- `wttr.in` requires no API key and supports city names, airport codes, or coordinates.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/garymjr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
