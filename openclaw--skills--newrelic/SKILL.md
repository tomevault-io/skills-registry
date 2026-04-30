---
name: newrelic
description: Monitor applications and infrastructure via New Relic API. Query metrics and manage alerts. Use when this capability is needed.
metadata:
  author: openclaw
---
# New Relic
Observability platform.
## Environment
```bash
export NEWRELIC_API_KEY="xxxxxxxxxx"
export NEWRELIC_ACCOUNT_ID="xxxxxxxxxx"
```
## Query NRQL
```bash
curl -X POST "https://api.newrelic.com/graphql" \
  -H "API-Key: $NEWRELIC_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": "{ actor { account(id: '$NEWRELIC_ACCOUNT_ID') { nrql(query: \"SELECT count(*) FROM Transaction\") { results } } } }"}'
```
## List Applications
```bash
curl "https://api.newrelic.com/v2/applications.json" -H "Api-Key: $NEWRELIC_API_KEY"
```
## Links
- Dashboard: https://one.newrelic.com
- Docs: https://docs.newrelic.com/docs/apis/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
