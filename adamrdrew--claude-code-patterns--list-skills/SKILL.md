---
name: list-skills
description: Lists all available skills with their names and descriptions. Use this skill first to discover what capabilities are available before invoking other skills. Use when this capability is needed.
metadata:
  author: adamrdrew
---

# Available Skills

## Weather Skills

**weather-api**
Reference documentation for the Open-Meteo free weather API. Use this skill to learn how to fetch weather forecasts using curl. The API requires latitude/longitude coordinates and returns JSON data. No API key required.

**zip-code-to-lat-and-long**
Convert US zip codes to latitude/longitude coordinates using free APIs. No API key required. Covers Zippopotam.us (simplest) and OpenStreetMap Nominatim (alternative) with curl examples.

**get-local-weather**
Get weather for a location. Combines zip-code-to-lat-and-long and weather-api skills to fetch weather data for US locations.

## Memory Skills

**create-memory**
Initialize the memory persistence system by creating memory.md if it doesn't exist. Safe to call multiple times - will not overwrite existing memory.

**read-memory**
Read all entries from the memory persistence system, or look up a specific key. Requires memory.md to exist.

**update-memory**
Add or update a key-value pair in the memory persistence system. Performs upsert - updates existing keys or adds new ones. Requires memory.md to exist.

## Meta Skills

**list-skills**
Lists all available skills with their names and descriptions. Use this skill first to discover what capabilities are available.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
