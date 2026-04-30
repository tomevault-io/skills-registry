---
name: weather-pollen
description: Weather and pollen reports for any location using free APIs. Get current conditions, forecasts, and pollen data. Use when this capability is needed.
metadata:
  author: sundial-org
---

# Weather and Pollen Skill

Get weather and pollen reports for any location using free APIs.

## Usage

When asked about weather or pollen in Anna, TX (or configured location), use the `weather_report` tool from this skill.

## Tools

### weather_report
Get weather and pollen data for a specified location.

**Args:**
- `includePollen` (boolean, default: true) - Include pollen data
- `location` (string, optional) - Location name to display (coordinates configured via env)

**Example:**
```json
{"includePollen": true, "location": "Anna, TX"}
```

## Configuration

Set location via environment variables (defaults for Anna, TX):
- `WEATHER_LAT` - Latitude (default: 33.3506)
- `WEATHER_LON` - Longitude (default: -96.3175)
- `WEATHER_LOCATION` - Location display name (default: "Anna, TX")

## APIs Used
- **Weather:** Open-Meteo (free, no API key)
- **Pollen:** Pollen.com (free, no API key)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
