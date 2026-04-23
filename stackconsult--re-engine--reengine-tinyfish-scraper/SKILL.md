---
name: reengine-tinyfish-scraper
description: Scrape data from a single URL using the TinyFish API. Use when this capability is needed.
metadata:
  author: stackconsult
---

# RE Engine TinyFish Scraper

## When to use
Use this skill when you need to scrape data from a single URL.

## Inputs
- `url`: The URL to scrape.
- `goal`: A natural language description of the data to be extracted.

## Procedure
1) Send a POST request to the TinyFish API with the URL and goal.
2) Stream the results back to the client.
3) Transform the data into a consistent format.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stackconsult) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
