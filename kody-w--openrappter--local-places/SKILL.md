---
name: local-places
description: Search for local places and businesses using Overpass (OpenStreetMap) API without API keys. Use when this capability is needed.
metadata:
  author: kody-w
---

# Local Places

Find nearby places using OpenStreetMap's Overpass API (no API key needed).

## Search Nearby Restaurants

```bash
curl -s "https://overpass-api.de/api/interpreter" --data-urlencode 'data=[out:json];node["amenity"="restaurant"](around:1000,37.7749,-122.4194);out body 10;' | jq '.elements[] | {name: .tags.name, cuisine: .tags.cuisine}'
```

## Find Cafes

```bash
curl -s "https://overpass-api.de/api/interpreter" --data-urlencode 'data=[out:json];node["amenity"="cafe"](around:500,37.7749,-122.4194);out body 5;' | jq '.elements[] | {name: .tags.name}'
```

## Search by Name

```bash
curl -s "https://nominatim.openstreetmap.org/search?q=Central+Park&format=json&limit=5" | jq '.[].display_name'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
