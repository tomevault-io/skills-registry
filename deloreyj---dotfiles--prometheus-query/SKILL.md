---
name: prometheus-query
description: Query Cloudflare Access-protected Prometheus and Thanos endpoints with promtool and the bundled promq CLI. Use when running PromQL, listing labels/series, or troubleshooting access tokens for metrics endpoints. Use when this capability is needed.
metadata:
  author: deloreyj
---

# Prometheus Query

## Overview

Use this skill to query Prometheus or Thanos endpoints protected by Cloudflare Access using promtool plus the bundled promq CLI. Rely on promq to fetch Access tokens, apply the correct header casing, and generate promtool HTTP config files.

## When to Use

- Use when asked to run PromQL against Cloudflare Access-protected Prometheus/Thanos endpoints
- Use when a repeatable login/query workflow is needed across multiple endpoints
- Use when listing metric names, label values, or series selectors with promtool

## Available Endpoints

| Endpoint | URL | Description |
| --- | --- | --- |
| Thanos (Global) | `https://metrics.cfdata.org` | Global Thanos query endpoint with all metrics |
| PDX Core | `https://pdx01.prometheus-access.cfdata.org` | PDX datacenter core metrics |
| Colo-specific | `https://{colo}01.prometheus-access.cfdata.org` | Per-colo metrics (e.g., sjc01, lax01, ams01) |

## Quick Start

1. Ensure `promtool` and `cloudflared` are installed and on PATH.
2. Run login to fetch a token and export it in the current shell:

```bash
eval "$(node skills/prometheus-query/scripts/promq.js login --endpoint https://metrics.cfdata.org)"
```

3. Run an instant query:

```bash
node skills/prometheus-query/scripts/promq.js query \
  --endpoint https://metrics.cfdata.org \
  'up{job="prometheus"}'
```

4. Reuse cached tokens without `eval` when needed (if cache is enabled):

```bash
node skills/prometheus-query/scripts/promq.js labels \
  --endpoint https://metrics.cfdata.org \
  __name__
```

## Command Reference

Run commands via `node skills/prometheus-query/scripts/promq.js <command>` or install locally with `npm --prefix skills/prometheus-query install` and use `npx --prefix skills/prometheus-query promq <command>`.

### login

Fetch an Access token and emit shell exports by default.

```bash
promq login --endpoint <url>
promq login --endpoint <url> --json
promq login --endpoint <url> --token
promq login --endpoint <url> --no-cache
```

### query (instant)

```bash
promq query --endpoint <url> '<promql>'
```

### range

```bash
promq range --endpoint <url> \
  --start="2024-01-01T00:00:00Z" \
  --end="2024-01-01T01:00:00Z" \
  --step=1m \
  '<promql>'
```

### labels

```bash
promq labels --endpoint <url> __name__
promq labels --endpoint <url> <label_name>
```

### series

```bash
promq series --endpoint <url> --match='{job="prometheus"}'
```

## Authentication Behavior

- Use `CF_ACCESS_TOKEN` or `PROMQ_ACCESS_TOKEN` to override cached tokens.
- Use `eval "$(promq login --endpoint <url>)"` to set `CF_ACCESS_TOKEN` and `PROMQ_ENDPOINT` in the current shell.
- Let promq choose the header case automatically: `CF-Access-Token` for `metrics.cfdata.org`, `cf-access-token` for all other endpoints.
- Use `PROMQ_DEFAULT_ENDPOINT` to change the default endpoint without passing `--endpoint`.
- Store cached tokens in `.opencode/skill/prometheus-query/token-cache.json` (relative to the working directory) unless `PROMQ_TOKEN_CACHE` overrides the path.

## Troubleshooting

- Fix "Sign in" HTML responses by re-running `login` and confirming header case.
- Fix "Invalid host header" by checking endpoint URL format and using `https://{colo}01.prometheus-access.cfdata.org`.
- Fix empty results by querying `up` first or listing `__name__` labels.
- Fix expired tokens by re-running `login` to refresh the cache.

## Resources

- `skills/prometheus-query/scripts/promq.js` - CLI wrapper around promtool and cloudflared
- `skills/prometheus-query/scripts/promq.test.js` - CLI unit tests
- `skills/prometheus-query/scripts/.env.example` - environment defaults and overrides

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deloreyj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
