---
name: get-kalshi-live-games
description: Fetch currently live Kalshi sports markets using the browser. Use when this capability is needed.
metadata:
  author: openclaw
---
# Skill: Get Kalshi Live Games

## Purpose
Fetch currently live Kalshi sports markets using the browser.

## Tool Policy
- MUST use the browser tool
- MUST NOT use web_search or web_fetch for live market data

## Steps
1. Start browser if not running.
2. Open https://kalshi.com
3. Navigate to live sports markets.
4. Extract up to 5 currently running games.
5. Return only:
   - Game name
   - Probability

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
