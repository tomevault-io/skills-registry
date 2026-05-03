---
name: swiss-weather
description: Retrieve current weather and today's forecast for Swiss locations using Open-Meteo APIs. Accepts city names or 4-digit Swiss postal codes. Use when this capability is needed.
metadata:
  author: peschee
---

# Swiss Weather (CH)

Retrieve current weather and today's forecast for Swiss locations using Open-Meteo.

## When to use
- User asks for weather in Switzerland.
- Input is a Swiss city/place name or 4-digit Swiss ZIP code.

## Inputs
- `query`: Place name or Swiss ZIP (e.g., `Zürich`, `8001`).

## APIs
- Geocoding: `https://geocoding-api.open-meteo.com/v1/search`
- Forecast: `https://api.open-meteo.com/v1/forecast`

## Steps
1. Detect ZIP vs place name.
   - ZIP pattern: `^\d{4}$` → treat as postal code.
   - Otherwise treat as place name.
2. Geocode with Switzerland filter:
   - `name=<query>`
   - `count=1`
   - `language=de`
   - `countryCode=CH`
3. If no results, return: `Kein Ort in der Schweiz gefunden für "<query>".`
4. Fetch weather using resolved `latitude` and `longitude`:
   - `timezone=auto`
   - `current=temperature_2m,apparent_temperature,weather_code,wind_speed_10m,precipitation`
   - `daily=temperature_2m_max,temperature_2m_min,weather_code,precipitation_sum,sunrise,sunset`
   - `forecast_days=1`
5. Map `weather_code` to a short German description (see table).
6. Output a concise summary in German (see template).

## Output template (German)
```
Wetter für <Ort>, Schweiz

Aktuell: <temp>°C (gefühlt <feels>°C), <Bedingung>
Wind: <wind> km/h | Niederschlag: <precip> mm

Tagesprognose:
  Höchst: <max>°C | Tiefst: <min>°C
  Bedingungen: <Bedingung>
  Niederschlag: <sum> mm erwartet
  Sonnenaufgang: <sunrise> | Sonnenuntergang: <sunset>
```

## WMO weather code mapping (concise)
- 0: Klar
- 1–3: Leicht bewölkt bis bedeckt
- 45, 48: Nebel
- 51–55: Nieselregen
- 61–65: Regen
- 71–75: Schnee
- 80–82: Regenschauer
- 95–99: Gewitter

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/peschee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
