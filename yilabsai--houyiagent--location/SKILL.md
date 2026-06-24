---
name: location
description: Location/geocoding tools using Open-Meteo Geocoding API Use when this capability is needed.
metadata:
  author: yilabsai
---

# Location Skill

Provides geocoding capabilities to convert city names to coordinates.

## Tools

### get_location

Get geographic coordinates for a city.

**Parameters:**
- `city` (str, optional): City name to geocode. Defaults to "Hangzhou".

**Returns:** Dictionary containing:
- `city`: City name
- `lat`: Latitude
- `lon`: Longitude
- `country`: Country name
- `country_code`: ISO country code
- `timezone`: Timezone identifier
- `population`: City population (if available)

**Examples:**
```
get_location("Beijing")
# Returns: {"city": "Beijing", "lat": 39.9042, "lon": 116.4074, "country": "China", ...}

get_location("Tokyo")
# Returns: {"city": "Tokyo", "lat": 35.6895, "lon": 139.6917, "country": "Japan", ...}

get_location()  # defaults to Hangzhou
# Returns: {"city": "Hangzhou", "lat": 30.2741, "lon": 120.1551, "country": "China", ...}
```

## Data Source

Uses [Open-Meteo Geocoding API](https://open-meteo.com/en/docs/geocoding-api) - free, no API key required.

## Usage with Weather Skill

Combine with weather skill to get weather for any city:

```python
location = get_location("Shanghai")
weather = get_weather(location["lat"], location["lon"], "today")
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yilabsai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
