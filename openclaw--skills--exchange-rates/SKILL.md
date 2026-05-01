---
name: exchange-rates
description: > Use when this capability is needed.
metadata:
  author: openclaw
---

# Exchange Rates (XE.com)

Fetch live mid-market exchange rates from XE.com via headless browser.

## Usage

```bash
node ~/clawd/skills/exchange-rates/scripts/xe-rate.mjs <FROM> <TO> [AMOUNT]
```

**Examples:**
```bash
node ~/clawd/skills/exchange-rates/scripts/xe-rate.mjs USD INR        # 1 USD → INR
node ~/clawd/skills/exchange-rates/scripts/xe-rate.mjs EUR USD 500    # 500 EUR → USD
node ~/clawd/skills/exchange-rates/scripts/xe-rate.mjs THB INR 1000   # 1000 THB → INR
```

**Output:** JSON with `amount`, `from`, `to`, `rate`, `converted`, `source`, `timestamp`

## Response Format

Present results cleanly:
- Show the converted amount prominently
- Include the unit rate (1 FROM = X TO)
- Mention source is XE.com mid-market rate
- For amounts > 1, show both unit rate and total conversion

## Notes

- Uses Playwright + Browserless (CDP) to scrape XE.com
- Falls back to exchangerate-api.com if XE fails
- Currency codes: standard 3-letter ISO 4217 (USD, INR, EUR, GBP, THB, JPY, etc.)
- Rates are mid-market (not buy/sell spreads)
- Script takes ~4-5 seconds per lookup (browser overhead)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
