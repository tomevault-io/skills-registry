---
name: weather-checker
description: Use when working with a simple weather checking skill that fetches current weather data.
metadata:
  author: HeadyZhang
---

# Weather Checker

Check the current weather for any location.

## Usage

```bash
python3 {baseDir}/scripts/check-weather.py "New York"
```

## How It Works

1. Takes a location name as input
2. Calls the OpenWeatherMap API
3. Returns temperature, conditions, and humidity

## Requirements

- An OpenWeatherMap API key set as `OPENWEATHER_API_KEY` environment variable
- Python 3.8+

---
> Source: [HeadyZhang/agent-audit](https://github.com/HeadyZhang/agent-audit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
