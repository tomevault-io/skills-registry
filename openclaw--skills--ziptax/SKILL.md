---
name: ziptax-sales-tax
description: > Use when this capability is needed.
metadata:
  author: openclaw
---

# ZipTax Sales Tax Lookup

## Setup

Set `ZIPTAX_API_KEY` env variable with your API key from https://platform.zip.tax (DEVELOP > API Keys).
Free tier gives 100 calls/month. **Never share your API key publicly.**

## Quick Start

### Address Lookup (most accurate)
```bash
curl -s "https://api.zip-tax.com/request/v60?address=200+Spectrum+Center+Drive+Irvine+CA+92618" \
  -H "X-API-KEY: $ZIPTAX_API_KEY"
```

### Postal Code Lookup
```bash
curl -s "https://api.zip-tax.com/request/v60?postalcode=92618" \
  -H "X-API-KEY: $ZIPTAX_API_KEY"
```

### Lat/Lng Lookup
```bash
curl -s "https://api.zip-tax.com/request/v60?lat=33.6525&lng=-117.7479" \
  -H "X-API-KEY: $ZIPTAX_API_KEY"
```

## Workflow

1. Determine lookup type: address (best), lat/lng, or postal code
2. Use **v60** (latest) for full jurisdiction breakdowns; use v10 for simple combined rate
3. Make GET request to `https://api.zip-tax.com/request/v60` with auth header
4. Check `metadata.response.code` — 100 means success
5. Read `taxSummaries[0].rate` for total sales tax rate
6. Read `baseRates` array for state/county/city/district breakdown
7. Check `service.taxable` and `shipping.taxable` for service/freight taxability

## Key Points

- **Address > Postal code**: Address gives one exact result; postal code returns all rates in that ZIP
- **Auth**: Header `X-API-KEY` or query param `key`
- **Rate format**: Decimal (0.0775 = 7.75%)
- **Response code 100** = success; check `metadata.response.code`
- **Metrics endpoint** (`/account/metrics`) does not count against quota

## Bundled Resources

- **`scripts/lookup.sh`** — CLI wrapper for quick lookups. Run with `--address`, `--lat`/`--lng`, `--postalcode`, or `--metrics`
- **`references/api-reference.md`** — Full API reference with all endpoints, response schemas, code samples, response codes, and SDK links. Read when you need endpoint details or response field definitions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
