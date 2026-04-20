---
name: hk-open-data
description: Comprehensive Hong Kong open data skill combining weather, transport, parking, and geographic data from HK government APIs. Use when the user asks general questions about Hong Kong that may involve multiple data sources, or when you need to combine weather + transport + parking + location data for a complete answer. Acts as a unified interface to all HK open data. Use when this capability is needed.
metadata:
  author: samsonllam
---

# HK Open Data Skill

Unified access to Hong Kong government open data. Combines weather, transport, parking, and geographic data. No API keys needed.

## Available Data Sources

| Data | API | Format |
|------|-----|--------|
| Weather & Forecasts | HK Observatory | JSON |
| KMB Bus (1,621 routes) | data.etabus.gov.hk | JSON |
| Citybus (416 routes) | rt.data.gov.hk | JSON |
| GMB Minibus (569 routes) | data.etagmb.gov.hk | JSON |
| NLB Lantau Bus (64 routes) | rt.data.gov.hk | JSON |
| MTR Train Schedules | rt.data.gov.hk | JSON |
| Light Rail (LRT) | rt.data.gov.hk | JSON |
| Location Search | geodata.gov.hk | JSON |
| Parking Vacancy (541+) | api.data.gov.hk | JSON |
| A&E Wait Times (18 hospitals) | ha.org.hk | JSON |
| RTHK News | rthk.hk | RSS/XML |
| HKMA Financial Data | api.hkma.gov.hk | JSON |
| LCSD Facilities (116+) | lcsd.gov.hk | JSON |
| Public Holidays | 1823.gov.hk | JSON |
| Mortgage Statistics | api.hkma.gov.hk | JSON |
| Sunrise/Sunset/Moonrise | HK Observatory | JSON |
| Lunar Calendar | HK Observatory | JSON |
| Historical Climate (since 1884) | HK Observatory | JSON |
| Historical Archives | api.data.gov.hk | JSON |

## Quick API Reference

### Weather
```bash
# Current conditions
curl -s "https://data.weather.gov.hk/weatherAPI/opendata/weather.php?dataType=rhrread&lang=en"
# 9-day forecast
curl -s "https://data.weather.gov.hk/weatherAPI/opendata/weather.php?dataType=fnd&lang=en"
# Warnings
curl -s "https://data.weather.gov.hk/weatherAPI/opendata/weather.php?dataType=warnsum&lang=en"
```

### Transport
```bash
# KMB routes & ETA
curl -s "https://data.etabus.gov.hk/v1/transport/kmb/route/"
curl -s "https://data.etabus.gov.hk/v1/transport/kmb/stop-eta/{STOP_ID}"
# Citybus
curl -s "https://rt.data.gov.hk/v2/transport/citybus/route/ctb"
# MTR
curl -s "https://rt.data.gov.hk/v1/transport/mtr/getSchedule.php?line={LINE}&sta={STATION}"
```

### Location
```bash
curl -s "https://geodata.gov.hk/gs/api/v1.0.0/locationSearch?q={QUERY}"
```

### Parking
```bash
curl -s "https://api.data.gov.hk/v1/carpark-info-vacancy?data=info,vacancy&vehicleTypes=privateCar"
```

### Hospital A&E
```bash
curl -s "https://www.ha.org.hk/opendata/aed/aedwtdata2-en.json"
```

### News (RTHK)
```bash
curl -sL "http://rthk9.rthk.hk/rthk/news/rss/e_expressnews_elocal.xml"
```

### Finance (HKMA)
```bash
# Interbank liquidity
curl -s "https://api.hkma.gov.hk/public/market-data-and-statistics/daily-monetary-statistics/daily-figures-interbank-liquidity"
# Exchange rates
curl -s "https://api.hkma.gov.hk/public/market-data-and-statistics/monthly-statistical-bulletin/er-ir/er-eeri-daily?pagesize=1"
```

### Facilities (LCSD)
```bash
curl -s "https://www.lcsd.gov.hk/datagovhk/facility/facility-sc.json"
```

### Public Holidays
```bash
curl -s "https://www.1823.gov.hk/common/ical/en.json"
```

### Mortgage Statistics
```bash
curl -s "https://api.hkma.gov.hk/public/market-data-and-statistics/monthly-statistical-bulletin/banking/residential-mortgage-survey?sortby=end_of_month&sortorder=desc&pagesize=1"
```

### Sunrise/Sunset & Lunar Calendar
```bash
curl -s "https://data.weather.gov.hk/weatherAPI/opendata/opendata.php?dataType=SRS&lang=en&rformat=json&year=2026"
curl -s "https://data.weather.gov.hk/weatherAPI/opendata/lunardate.php?date=20260217"
```

### Historical Data
```bash
curl -s "https://api.data.gov.hk/v1/historical-archive/list-file-versions?url={RESOURCE_URL}&start={YYYYMMDD}&end={YYYYMMDD}"
```

## Multi-Source Queries

For questions that span multiple data types, query relevant APIs in parallel and combine:

**Example**: "我想去中環，天氣點？有冇車位？"
1. Weather: `dataType=rhrread` → current conditions
2. GeoData: `locationSearch?q=central` → get coordinates
3. Parking: `carpark-info-vacancy?data=info,vacancy&vehicleTypes=privateCar` → filter by Central district
4. Present combined answer

## Detailed Documentation

For complete API specs, response schemas, and usage patterns for each data source, install the individual skills:
- `hk-weather` — Full HKO API documentation
- `hk-transport` — KMB/Citybus/GMB/NLB/MTR/LRT endpoints and station codes
- `hk-parking` — Parking vacancy API and parsing
- `hk-geodata` — Location search and coordinate conversion
- `hk-hospital` — A&E wait times and triage categories
- `hk-news` — RTHK news RSS feeds
- `hk-finance` — HKMA monetary statistics
- `hk-facilities` — LCSD sports and leisure venues
- `hk-holidays` — Public holidays calendar
- `hk-mortgage` — HKMA residential mortgage data

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/samsonllam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
