---
name: statuspage
description: Manage Statuspage incidents and components via API. Update status and communicate outages. Use when this capability is needed.
metadata:
  author: openclaw
---
# Statuspage
Status communication.
## Environment
```bash
export STATUSPAGE_API_KEY="xxxxxxxxxx"
export STATUSPAGE_PAGE_ID="xxxxxxxxxx"
```
## Create Incident
```bash
curl -X POST "https://api.statuspage.io/v1/pages/$STATUSPAGE_PAGE_ID/incidents" \
  -H "Authorization: OAuth $STATUSPAGE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"incident": {"name": "Service Degradation", "status": "investigating", "body": "We are investigating..."}}'
```
## List Incidents
```bash
curl "https://api.statuspage.io/v1/pages/$STATUSPAGE_PAGE_ID/incidents" \
  -H "Authorization: OAuth $STATUSPAGE_API_KEY"
```
## Update Component
```bash
curl -X PATCH "https://api.statuspage.io/v1/pages/$STATUSPAGE_PAGE_ID/components/{id}" \
  -H "Authorization: OAuth $STATUSPAGE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"component": {"status": "operational"}}'
```
## Links
- Docs: https://developer.statuspage.io

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
