---
name: logtrail-api
description: Integration guide for the Logtrail Workspace API. Use when Gemini CLI needs to ingest logs, query log data, or configure Logtrail SDKs in a project. Use when this capability is needed.
metadata:
  author: logtrailnet
---

# Logtrail API Skill

This skill provides the necessary knowledge and patterns to integrate Logtrail's high-performance logging into any application.

## Core Workflows

### 1. Ingesting Logs
To send logs to Logtrail, use the `/logs` endpoint.
- **Header**: `X-API-Key`
- **Payload**: JSON matching the `CreateLogRequest` schema in `references/openapi.yaml`.

Example cURL:
```bash
curl -X POST https://api.logtrail.net/api/v1/workspace/logs \
  -H "X-API-Key: $LOGTRAIL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"action": "test", "level": "info", "clientTimestamp": "2026-02-14T00:00:00Z"}'
```

### 2. Querying logs with LCQL
Use the Logtrail Custom Query Language (LCQL) for advanced searches.
- **Endpoint**: `POST /logs/query`
- **Body**: `{ "query": "..." }`

**LCQL Syntax:**
- `level=error` (Facet)
- `actor.id=123` (Nested JSONB)
- `action~"login*"` (Partial match)
- `message@"timeout"` (Full-text)

## References
- [openapi.yaml](references/openapi.yaml): Full API specification including all schemas and error codes.

## Implementation Patterns
When adding Logtrail to a project:
1. Identify the logging framework (e.g., Zap, Pino).
2. Configure the Logtrail HTTP transport or use an official SDK.
3. Use the `test` environment for development and `live` for production via scoped API keys.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/logtrailnet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
