---
name: zip-code-to-lat-and-long
description: Convert US zip codes to latitude/longitude coordinates using free APIs. No API key required. Covers Zippopotam.us (simplest) and OpenStreetMap Nominatim (alternative) with curl examples. Use when this capability is needed.
metadata:
  author: adamrdrew
---

# Zip Code to Latitude/Longitude APIs

Two free options for converting US zip codes to coordinates, no API key required.

## Option 1: Zippopotam.us (Recommended)

The simplest option - purpose-built for zip code lookups. No authentication, no rate limits documented, CORS enabled.

### Base URL

```
https://api.zippopotam.us
```

### Endpoint Format

```
GET /us/{zip_code}
```

### Example CURL

```bash
curl "https://api.zippopotam.us/us/90210"
```

### Response Format

```json
{
  "post code": "90210",
  "country": "United States",
  "country abbreviation": "US",
  "places": [
    {
      "place name": "Beverly Hills",
      "longitude": "-118.4065",
      "state": "California",
      "state abbreviation": "CA",
      "latitude": "34.0901"
    }
  ]
}
```

### Extracting Coordinates with jq

```bash
# Get latitude
curl -s "https://api.zippopotam.us/us/10001" | jq -r '.places[0].latitude'

# Get longitude
curl -s "https://api.zippopotam.us/us/10001" | jq -r '.places[0].longitude'

# Get both as comma-separated
curl -s "https://api.zippopotam.us/us/10001" | jq -r '.places[0] | "\(.latitude),\(.longitude)"'
```

### Response Fields

| Field | Description |
|-------|-------------|
| `post code` | The zip code queried |
| `country` | Full country name |
| `country abbreviation` | 2-letter country code (US) |
| `places` | Array of locations (usually 1 for zip codes) |
| `places[].place name` | City/town name |
| `places[].state` | Full state name |
| `places[].state abbreviation` | 2-letter state code |
| `places[].latitude` | Latitude as string |
| `places[].longitude` | Longitude as string |

### Notes

- Returns 404 for invalid zip codes
- Coordinates are returned as strings, convert to float if needed
- Data sourced from GeoNames
- Some zip codes may have multiple places in the array

---

## Option 2: OpenStreetMap Nominatim

More powerful geocoding API, but requires a User-Agent header and has rate limits.

### Base URL

```
https://nominatim.openstreetmap.org
```

### Endpoint Format

```
GET /search?postalcode={zip}&country=USA&format=json
```

### Example CURL

```bash
curl -H "User-Agent: MyApp/1.0 (contact@example.com)" \
  "https://nominatim.openstreetmap.org/search?postalcode=90210&country=USA&format=json"
```

### Response Format

```json
[
  {
    "place_id": 123456,
    "licence": "Data © OpenStreetMap contributors, ODbL 1.0.",
    "lat": "34.0901",
    "lon": "-118.4065",
    "display_name": "90210, Beverly Hills, Los Angeles County, California, United States",
    "type": "postcode",
    "importance": 0.435
  }
]
```

### Extracting Coordinates with jq

```bash
curl -s -H "User-Agent: MyApp/1.0" \
  "https://nominatim.openstreetmap.org/search?postalcode=10001&country=USA&format=json" \
  | jq -r '.[0] | "\(.lat),\(.lon)"'
```

### Important Requirements

| Requirement | Details |
|-------------|---------|
| User-Agent | **Required** - Must identify your application |
| Rate Limit | Max 1 request per second |
| No Bulk Queries | Cannot download complete lists of postcodes |

### When to Use Nominatim

- Need additional address details
- Need international postal codes beyond Zippopotam coverage
- Building an application that also needs general geocoding

---

## Comparison

| Feature | Zippopotam.us | Nominatim |
|---------|---------------|-----------|
| API Key | Not required | Not required |
| User-Agent | Not required | **Required** |
| Rate Limit | None documented | 1 req/sec |
| US Coverage | 43,624 zip codes | Comprehensive |
| Response | Simple JSON | Detailed JSON |
| Best For | Simple zip lookups | General geocoding |

## Recommendation

Use **Zippopotam.us** for simple US zip code to lat/long conversion. It's simpler, has no documented rate limits, and returns a clean, predictable response format.

Use **Nominatim** if you need additional geocoding capabilities beyond zip codes, or need the extra address details in the response.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
