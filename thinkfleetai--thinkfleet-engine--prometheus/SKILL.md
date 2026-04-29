---
name: prometheus
description: Query metrics via Prometheus API. Run PromQL queries and manage alerts. Use when this capability is needed.
metadata:
  author: thinkfleetai
---
# Prometheus
Metrics and monitoring.
## Environment
```bash
export PROMETHEUS_URL="http://prometheus.example.com:9090"
```
## Query Metrics
```bash
curl "$PROMETHEUS_URL/api/v1/query?query=up"
```
## Range Query
```bash
curl "$PROMETHEUS_URL/api/v1/query_range?query=rate(http_requests_total[5m])&start=2024-01-30T00:00:00Z&end=2024-01-30T12:00:00Z&step=60"
```
## List Targets
```bash
curl "$PROMETHEUS_URL/api/v1/targets"
```
## List Alert Rules
```bash
curl "$PROMETHEUS_URL/api/v1/rules"
```
## Get Alerts
```bash
curl "$PROMETHEUS_URL/api/v1/alerts"
```
## Links
- Docs: https://prometheus.io/docs/prometheus/latest/querying/api/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
