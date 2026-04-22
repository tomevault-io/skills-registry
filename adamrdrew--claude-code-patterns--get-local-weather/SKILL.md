---
name: get-local-weather
description: Get the 7-day weather forecast for the user's location. Reads zip code from memory, converts to coordinates, and fetches weather data. Requires memory to be initialized with a zip_code entry. Use when this capability is needed.
metadata:
  author: adamrdrew
---

# Get Local Weather

Fetch the 7-day weather forecast using the zip code stored in memory.

## Prerequisites

- Memory must be initialized (`memory.md` must exist)
- Memory must contain a `zip_code` entry

## Procedure

### Step 1: Read Zip Code from Memory

Use the `read-memory` skill to look up the `zip_code` key.

**If memory.md does not exist:**
- Stop execution
- Inform the user: "Memory has not been initialized. Use the `create-memory` skill first, then use `update-memory` to add your zip code."
- Do NOT proceed further

**If zip_code is not found in memory:**
- Stop execution
- Inform the user: "No zip code found in memory. Use the `update-memory` skill to add your zip code. For example: 'Remember my zip code is 90210'"
- Do NOT proceed further

### Step 2: Convert Zip Code to Coordinates

Use the Zippopotam.us API (documented in `zip-code-to-lat-and-long` skill) to convert the zip code to latitude/longitude.

```bash
curl -s "https://api.zippopotam.us/us/{zip_code}" | jq -r '.places[0] | "\(.latitude),\(.longitude)"'
```

**If the API returns 404 or no results:**
- Inform the user the zip code appears to be invalid
- Suggest they update their zip code in memory
- Do NOT proceed further

Note the city and state from the response for the weather report.

### Step 3: Fetch Weather Data

Use the Open-Meteo API (documented in `weather-api` skill) to get the 7-day forecast.

```bash
curl -s "https://api.open-meteo.com/v1/forecast?latitude={lat}&longitude={lon}&daily=temperature_2m_max,temperature_2m_min,precipitation_probability_max,weather_code&temperature_unit=fahrenheit&precipitation_unit=inch&timezone=auto"
```

### Step 4: Present Weather Report

Format the weather data as a readable report:

```
## 7-Day Weather Forecast for {City}, {State}

| Day | High | Low | Precip % | Conditions |
|-----|------|-----|----------|------------|
| Mon | 72°F | 55°F | 10% | Clear |
| Tue | 68°F | 52°F | 45% | Rain |
...
```

Use the WMO weather codes to translate conditions:
- 0: Clear sky
- 1-3: Partly cloudy
- 45, 48: Fog
- 51-55: Drizzle
- 61-65: Rain
- 71-75: Snow
- 80-82: Rain showers
- 95-99: Thunderstorm

## Important Constraints

- **Read-only:** This skill does NOT modify memory
- **No storage:** This skill does NOT store weather data in memory
- **Fail fast:** Stop immediately if prerequisites are not met

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
