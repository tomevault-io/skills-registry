---
name: weather-api
description: Reference documentation for the Open-Meteo free weather API. Use this skill to learn how to fetch weather forecasts using curl. The API requires latitude/longitude coordinates and returns JSON data. No API key required. Use when this capability is needed.
metadata:
  author: adamrdrew
---

# Open-Meteo Weather API Reference

Open-Meteo is a free, open-source weather API. No API key, registration, or credit card required for non-commercial use.

## Base URL

```
https://api.open-meteo.com/v1/forecast
```

## Required Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `latitude` | float | WGS84 latitude coordinate (e.g., 40.7128 for New York) |
| `longitude` | float | WGS84 longitude coordinate (e.g., -74.0060 for New York) |

## Common US City Coordinates

| City | Latitude | Longitude |
|------|----------|-----------|
| New York, NY | 40.7128 | -74.0060 |
| Los Angeles, CA | 34.0522 | -118.2437 |
| Chicago, IL | 41.8781 | -87.6298 |
| Houston, TX | 29.7604 | -95.3698 |
| Phoenix, AZ | 33.4484 | -112.0740 |
| Seattle, WA | 47.6062 | -122.3321 |
| Denver, CO | 39.7392 | -104.9903 |
| Miami, FL | 25.7617 | -80.1918 |

## Useful Optional Parameters

| Parameter | Values | Description |
|-----------|--------|-------------|
| `hourly` | comma-separated list | Hourly weather variables to return |
| `daily` | comma-separated list | Daily weather variables to return |
| `temperature_unit` | `celsius` (default), `fahrenheit` | Temperature unit |
| `wind_speed_unit` | `kmh` (default), `ms`, `mph`, `kn` | Wind speed unit |
| `precipitation_unit` | `mm` (default), `inch` | Precipitation unit |
| `timezone` | `auto`, `America/New_York`, etc. | Timezone for timestamps |
| `forecast_days` | 1-16 | Number of forecast days (default: 7) |

## Hourly Variables

Common hourly variables for `hourly=` parameter:

- `temperature_2m` - Air temperature at 2m height
- `relative_humidity_2m` - Relative humidity at 2m
- `apparent_temperature` - Feels-like temperature
- `precipitation` - Total precipitation (rain + snow)
- `rain` - Rain only
- `snowfall` - Snowfall
- `weather_code` - WMO weather condition code
- `cloud_cover` - Total cloud cover percentage
- `wind_speed_10m` - Wind speed at 10m height
- `wind_direction_10m` - Wind direction at 10m
- `wind_gusts_10m` - Wind gusts at 10m
- `visibility` - Visibility in meters
- `uv_index` - UV index

## Daily Variables

Common daily variables for `daily=` parameter:

- `temperature_2m_max` - Maximum daily temperature
- `temperature_2m_min` - Minimum daily temperature
- `apparent_temperature_max` - Maximum feels-like temperature
- `apparent_temperature_min` - Minimum feels-like temperature
- `precipitation_sum` - Total daily precipitation
- `precipitation_probability_max` - Maximum precipitation probability
- `weather_code` - WMO weather condition code
- `sunrise` - Sunrise time
- `sunset` - Sunset time
- `wind_speed_10m_max` - Maximum wind speed
- `uv_index_max` - Maximum UV index

## WMO Weather Codes

| Code | Description |
|------|-------------|
| 0 | Clear sky |
| 1, 2, 3 | Mainly clear, partly cloudy, overcast |
| 45, 48 | Fog |
| 51, 53, 55 | Drizzle (light, moderate, dense) |
| 61, 63, 65 | Rain (slight, moderate, heavy) |
| 71, 73, 75 | Snow fall (slight, moderate, heavy) |
| 80, 81, 82 | Rain showers (slight, moderate, violent) |
| 95 | Thunderstorm |
| 96, 99 | Thunderstorm with hail |

## Example CURL Commands

### Basic 7-Day Forecast (Daily High/Low)

```bash
curl "https://api.open-meteo.com/v1/forecast?latitude=40.7128&longitude=-74.0060&daily=temperature_2m_max,temperature_2m_min,weather_code&temperature_unit=fahrenheit&timezone=America/New_York"
```

### 7-Day Forecast with Precipitation

```bash
curl "https://api.open-meteo.com/v1/forecast?latitude=40.7128&longitude=-74.0060&daily=temperature_2m_max,temperature_2m_min,precipitation_sum,precipitation_probability_max,weather_code&temperature_unit=fahrenheit&precipitation_unit=inch&timezone=America/New_York"
```

### Hourly Forecast for Next 24 Hours

```bash
curl "https://api.open-meteo.com/v1/forecast?latitude=40.7128&longitude=-74.0060&hourly=temperature_2m,precipitation,weather_code&temperature_unit=fahrenheit&timezone=America/New_York&forecast_days=1"
```

### Complete Daily Forecast

```bash
curl "https://api.open-meteo.com/v1/forecast?latitude=40.7128&longitude=-74.0060&daily=temperature_2m_max,temperature_2m_min,apparent_temperature_max,apparent_temperature_min,precipitation_sum,precipitation_probability_max,weather_code,sunrise,sunset,wind_speed_10m_max,uv_index_max&temperature_unit=fahrenheit&wind_speed_unit=mph&precipitation_unit=inch&timezone=America/New_York"
```

### Current Conditions (Use hourly with forecast_days=1)

```bash
curl "https://api.open-meteo.com/v1/forecast?latitude=40.7128&longitude=-74.0060&hourly=temperature_2m,relative_humidity_2m,apparent_temperature,weather_code,wind_speed_10m,wind_direction_10m&temperature_unit=fahrenheit&wind_speed_unit=mph&timezone=America/New_York&forecast_days=1"
```

## Example JSON Response Structure

```json
{
  "latitude": 40.71,
  "longitude": -74.0,
  "timezone": "America/New_York",
  "daily": {
    "time": ["2024-01-15", "2024-01-16", "2024-01-17", ...],
    "temperature_2m_max": [42.5, 38.2, 35.1, ...],
    "temperature_2m_min": [32.1, 28.4, 25.0, ...],
    "weather_code": [3, 61, 71, ...]
  },
  "daily_units": {
    "temperature_2m_max": "°F",
    "temperature_2m_min": "°F",
    "weather_code": "wmo code"
  }
}
```

## Tips

1. Always use `timezone=auto` or specify a timezone to get local times
2. Use `temperature_unit=fahrenheit` for US users
3. Use `wind_speed_unit=mph` and `precipitation_unit=inch` for US units
4. Parse the `time` array to match up with weather values at the same index
5. Use `jq` to parse JSON responses in bash scripts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
