---
name: elasticsearch
description: Interact with Elasticsearch clusters via the API. Use when this capability is needed.
metadata:
  author: olasunkanmi-SE
---

# Elasticsearch

Use the provided `es-cli` wrapper or direct REST API calls to manage indexes and search data.

## CLI Usage (Recommended)

An `es-cli` wrapper is available in `.codebuddy/bin`.

Base URL default: `http://localhost:9200` (Override with ES_URL env var)

### Examples

- Check Health: `es-cli GET /_cluster/health`
- List Indexes: `es-cli GET /_cat/indices`
- Search: `es-cli GET /my-index/_search`

## Manual Connection

Typically accessed via `curl`.
Base URL: `http://localhost:9200` (or your cluster URL)

## Common Commands

### Cluster Health

```bash
curl -X GET "localhost:9200/_cluster/health?pretty"
```

### List Indexes

```bash
curl -X GET "localhost:9200/_cat/indices?v"
```

### Search

```bash
curl -X GET "localhost:9200/<index>/_search?q=*&pretty"
```

### Create Index

```bash
curl -X PUT "localhost:9200/<index>"
```

### Delete Index

```bash
curl -X DELETE "localhost:9200/<index>"
```

## Setup

Ensure you have access to the Elasticsearch cluster and `curl` installed.
For authenticated clusters, use `-u username:password` or Authorization headers.

---
> Source: [olasunkanmi-SE/codebuddy](https://github.com/olasunkanmi-SE/codebuddy) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
