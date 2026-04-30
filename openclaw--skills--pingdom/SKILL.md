---
name: pingdom
description: Monitor uptime and performance via Pingdom API. Manage checks and view reports. Use when this capability is needed.
metadata:
  author: openclaw
---
# Pingdom
Uptime monitoring.
## Environment
```bash
export PINGDOM_API_TOKEN="xxxxxxxxxx"
```
## List Checks
```bash
curl "https://api.pingdom.com/api/3.1/checks" -H "Authorization: Bearer $PINGDOM_API_TOKEN"
```
## Get Check Results
```bash
curl "https://api.pingdom.com/api/3.1/results/{checkId}" -H "Authorization: Bearer $PINGDOM_API_TOKEN"
```
## Create Check
```bash
curl -X POST "https://api.pingdom.com/api/3.1/checks" \
  -H "Authorization: Bearer $PINGDOM_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name": "My Website", "host": "example.com", "type": "http"}'
```
## Links
- Docs: https://docs.pingdom.com/api/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
