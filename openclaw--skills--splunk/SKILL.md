---
name: splunk
description: Search and analyze machine data via Splunk API. Run searches and manage dashboards. Use when this capability is needed.
metadata:
  author: openclaw
---
# Splunk
Data analytics and SIEM.
## Environment
```bash
export SPLUNK_URL="https://splunk.example.com:8089"
export SPLUNK_TOKEN="xxxxxxxxxx"
```
## Run Search
```bash
curl -X POST "$SPLUNK_URL/services/search/jobs" \
  -H "Authorization: Bearer $SPLUNK_TOKEN" \
  -d "search=search index=main | head 10"
```
## Get Search Results
```bash
curl "$SPLUNK_URL/services/search/jobs/{sid}/results?output_mode=json" \
  -H "Authorization: Bearer $SPLUNK_TOKEN"
```
## List Saved Searches
```bash
curl "$SPLUNK_URL/services/saved/searches?output_mode=json" \
  -H "Authorization: Bearer $SPLUNK_TOKEN"
```
## Links
- Docs: https://docs.splunk.com/Documentation/Splunk/latest/RESTREF/RESTprolog

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
