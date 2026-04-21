---
name: databuddy-api
description: Public API endpoints for feature flags and event tracking. Use when this capability is needed.
metadata:
  author: databuddy-analytics
---
# REST API

Public API endpoints for feature flags and event tracking.

## Feature Flags API

**Base URL:** `https://api.databuddy.cc/public/v1/flags`

All flag endpoints return `Cache-Control: public, max-age=15, s-maxage=30, stale-while-revalidate=15`.

### GET `/evaluate` — Evaluate a Single Flag

Query params:

| Param | Required | Description |
|-------|----------|-------------|
| `key` | Yes | Flag key |
| `clientId` | Yes | Website or organization client ID |
| `userId` | No | User ID for targeting |
| `email` | No | User email for targeting |
| `organizationId` | No | Organization ID for targeting |
| `teamId` | No | Team ID for targeting |
| `properties` | No | JSON string of custom properties |
| `environment` | No | Target environment |

Response: `FlagResult`

```json
{
  "enabled": true,
  "value": true,
  "variant": "v2",
  "payload": { "discount": 20 },
  "reason": "USER_RULE_MATCH"
}
```

**Reason codes:** `FLAG_NOT_FOUND`, `MISSING_REQUIRED_PARAMS`, `USER_RULE_MATCH`, `TARGET_GROUP_MATCH`, `MULTIVARIANT_EVALUATED`, `ROLLOUT_ENABLED`, `ROLLOUT_DISABLED`, `BOOLEAN_DEFAULT`, `DEFAULT_VALUE`, `EVALUATION_ERROR`

### GET `/bulk` — Evaluate Multiple Flags

Query params: same as `/evaluate` except `key` is replaced by:

| Param | Required | Description |
|-------|----------|-------------|
| `clientId` | Yes | Website or organization client ID |
| `keys` | No | Comma-separated flag keys (omit for all flags) |

Response:

```json
{
  "flags": {
    "dark-mode": { "enabled": true, "value": true, "payload": null, "reason": "BOOLEAN_DEFAULT" },
    "pricing-tier": { "enabled": true, "value": "pro", "variant": "pro", "payload": null, "reason": "MULTIVARIANT_EVALUATED" }
  },
  "count": 2
}
```

### GET `/definitions` — List Flag Definitions

Query params:

| Param | Required | Description |
|-------|----------|-------------|
| `clientId` | Yes | Website or organization client ID |
| `environment` | No | Target environment |

Response:

```json
{
  "flags": [
    { "key": "dark-mode", "description": "...", "type": "boolean", "variants": null },
    { "key": "pricing-tier", "description": "...", "type": "multivariant", "variants": [...] }
  ],
  "count": 2
}
```

### GET `/health` — Health Check

Response: `{ "service": "flags", "status": "ok", "version": "1.0.0" }`

---

## Event Tracking API

**Base URL:** `https://basket.databuddy.cc`

### POST `/track` — Send Events

Accepts a single event object or an array of up to 100 events.

**Authentication** (one of):
- `Authorization: Bearer $DATABUDDY_API_KEY` header (API key with `track:events` scope)
- `?website_id=xxx` query param (public tracking via website ID)

**Event schema:**

```json
{
  "name": "purchase",
  "namespace": "billing",
  "timestamp": 1712345678000,
  "properties": { "amount": 99.99, "currency": "USD" },
  "anonymousId": "anon_abc123",
  "sessionId": "sess_xyz",
  "websiteId": "web_123",
  "source": "backend"
}
```

| Field | Type | Limits | Required |
|-------|------|--------|----------|
| `name` | `string` | 1-256 chars | Yes |
| `namespace` | `string` | max 64 chars | No |
| `timestamp` | `number \| string \| Date` | — | No (auto-set) |
| `properties` | `Record<string, unknown>` | — | No |
| `anonymousId` | `string` | max 256 chars | No |
| `sessionId` | `string` | max 256 chars | No |
| `websiteId` | `string` | — | No |
| `source` | `string` | max 64 chars | No |

**Batch:** Send an array of events (max 100 items).

**Limits:**
- Max payload: 1MB (single), 5MB (batch)
- Max batch size: 100 events

**Response:**

```json
{ "status": "success", "type": "custom_event", "count": 1 }
```

### Examples

**Single event with API key:**

```bash
curl -X POST https://basket.databuddy.cc/track \
  -H "Authorization: Bearer $DATABUDDY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name": "purchase", "properties": {"amount": 99.99}}'
```

**Single event with website ID:**

```bash
curl -X POST "https://basket.databuddy.cc/track?website_id=$DATABUDDY_CLIENT_ID" \
  -H "Content-Type: application/json" \
  -d '{"name": "page_view", "properties": {"path": "/pricing"}}'
```

**Batch events:**

```bash
curl -X POST https://basket.databuddy.cc/track \
  -H "Authorization: Bearer $DATABUDDY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '[
    {"name": "event_1", "properties": {"key": "value"}},
    {"name": "event_2", "properties": {"key": "value"}}
  ]'
```

> Always read API keys from environment variables. Never hardcode credentials in code or commands.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/databuddy-analytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
