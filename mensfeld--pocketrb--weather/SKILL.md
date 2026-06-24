---
name: weather
description: Get weather forecasts using wttr.in (no API key required) Use when this capability is needed.
metadata:
  author: mensfeld
---

# Weather Skill

Get weather data using wttr.in - a console-oriented weather service that requires no API key.

## Quick Commands

### Current Weather

```bash
# Simple one-line format
curl -s "wttr.in/London?format=3"
# Output: London: ⛅️ +8°C

# With more details
curl -s "wttr.in/London?format=%l:+%c+%t+%h+%w"
# Output: London: ⛅️ +8°C 65% ↙15km/h

# Current conditions only
curl -s "wttr.in/London?0"
```

### Full Forecast

```bash
# 3-day forecast (default)
curl -s "wttr.in/London"

# 1-day forecast
curl -s "wttr.in/London?1"

# 2-day forecast
curl -s "wttr.in/London?2"
```

### Format Options

```bash
# Metric units (Celsius, km/h)
curl -s "wttr.in/London?m"

# Imperial units (Fahrenheit, mph)
curl -s "wttr.in/London?u"

# Compact format (no terminal colors)
curl -s "wttr.in/London?T"

# Plain text (no ANSI)
curl -s "wttr.in/London?T"
```

## Custom Format Strings

Use `format=` for custom output:

| Symbol | Meaning |
|--------|---------|
| %c | Weather condition icon |
| %C | Weather condition text |
| %t | Temperature |
| %f | "Feels like" temperature |
| %h | Humidity |
| %w | Wind |
| %l | Location |
| %m | Moon phase |
| %p | Precipitation (mm) |
| %P | Pressure |
| %S | Sunrise |
| %s | Sunset |

### Examples

```bash
# Minimal
curl -s "wttr.in/Tokyo?format=%c+%t"
# ☀️ +15°C

# Detailed
curl -s "wttr.in/Tokyo?format=%l:+%C,+%t+(%f),+%h+humidity,+wind+%w"
# Tokyo: Sunny, +15°C (+13°C), 45% humidity, wind ↗12km/h

# JSON format
curl -s "wttr.in/Tokyo?format=j1" | jq '.current_condition[0].temp_C'
```

## Location Formats

```bash
# City name
curl -s "wttr.in/Paris"

# City, Country
curl -s "wttr.in/Paris,France"

# Airport code
curl -s "wttr.in/JFK"

# Coordinates
curl -s "wttr.in/51.5,-0.1"

# IP-based (auto-detect)
curl -s "wttr.in"

# Landmark
curl -s "wttr.in/Eiffel+Tower"
```

## Tips

- Add `?lang=XX` for different languages (e.g., `?lang=de` for German)
- Use `?n` to disable colors in narrow terminals
- Append `?Q` for quiet mode (no "Weather report" header)
- For scripts, use `?format=j1` for full JSON data
- Moon phase: `curl -s "wttr.in/Moon"`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mensfeld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
