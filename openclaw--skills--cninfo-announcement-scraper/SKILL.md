---
name: cninfo-announcement-scraper
description: Use this skill to pull CNINFO official disclosures and extract positive catalysts for A-share monitoring.
metadata:
  author: openclaw
---
# cninfo-announcement-scraper

Use this skill to pull CNINFO official disclosures and extract positive catalysts for A-share monitoring.

## Inputs
- Time window (default: current trading day)
- Event filter (default: 业绩预增, 重大合同, 政策扶持)
- Industry filter (default: 新能源, 消费)

## Procedure
1. Fetch latest CNINFO announcements.
2. Parse and normalize: code, company, title, announcement_time, url.
3. Classify announcement type.
4. Keep only allowed event types and industries.
5. Return structured rows for alert generation.

## Output Schema
- ticker
- company
- catalyst
- source_url
- timestamp
- confidence
- trigger_price
- invalidation_condition

## Safety
- Use official disclosure links only.
- Never provide auto-trade instructions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
