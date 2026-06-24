---
name: tidb-zero
description: Create and use TiDB Zero temporary/playground TiDB databases via unauthenticated REST API using curl (no tidbcloud-manager / no pip). TRIGGER: tidb0, ti0, tidb-zero, instant tidb cluster, temporary tidb database, playground, temp, 快速创建临时集群, 临时集群. Supports 1 operation: create a temporary TiDB database/cluster. After creation, always print id, name, connection(host, port, username, password), expiresAt, remainingDatabaseQuota. For follow-up DB tasks, run SQL using mysqlsh or mysql CLI with the returned connection. Use when this capability is needed.
metadata:
  author: tidbcloud
---

# TiDB Zero (Temporary / Playground Database)

## Goal

Create a temporary/playground TiDB database via **one** HTTP API.

## Setup (env + tools)

Required env:
- `TIDBZERO_HOST`: API host (no auth)

Optional env:
- `TIDBZERO_BASE_PATH` (default `/v1alpha1`)
- `TIDB0_NAME_PREFIX` (optional)
- `TIDB0_DB_PASSWORD` (optional)

Timeout env:
- `TIDB0_CURL_CONNECT_TIMEOUT` (default `10`)
- `TIDB0_CURL_MAX_TIME` (default `60`)

Prereqs:
- HTTP: `curl` (and optionally `jq` for pretty-print)
- SQL: `mysqlsh` (preferred) or `mysql`

## API: Create a temporary database (playground-like)

Base URL:
```bash
BASE="https://${TIDBZERO_HOST}${TIDBZERO_BASE_PATH:-/v1alpha1}"
```

Request body (optional: `namePrefix`, `password`):
```bash
body='{"namePrefix":"'"${TIDB0_NAME_PREFIX:-}"'"}'
```

If you want to set a password (optional):
```bash
body='{"namePrefix":"'"${TIDB0_NAME_PREFIX:-}"'","password":"'"${TIDB0_DB_PASSWORD}"'"}'
```

Call (current spec uses POST `/instances`):
```bash
curl -sS -X POST \
  -H 'content-type: application/json' \
  --connect-timeout "${TIDB0_CURL_CONNECT_TIMEOUT:-10}" \
  --max-time "${TIDB0_CURL_MAX_TIME:-60}" \
  --data-binary "$body" \
  "${BASE}/instances"
```

If you want pretty JSON (optional `jq`):
```bash
curl -sS -X POST \
  -H 'content-type: application/json' \
  --connect-timeout "${TIDB0_CURL_CONNECT_TIMEOUT:-10}" \
  --max-time "${TIDB0_CURL_MAX_TIME:-60}" \
  --data-binary "$body" \
  "${BASE}/instances" | jq .
```

On success, ALWAYS explicitly print these fields to the user (in addition to the full JSON response):
- `id`, `name`, `expiresAt`, `remainingDatabaseQuota`
- `connection.host`, `connection.port`, `connection.username`, `connection.password`

## Follow-up: Run SQL on the created database

Use returned `connection`.

mysqlsh (preferred; password via stdin). In sandboxed environments, set `MYSQLSH_USER_CONFIG_HOME` to a writable directory:
```bash
mkdir -p /tmp/mysqlsh-codex
printf '%s' '<connection.password>' | MYSQLSH_USER_CONFIG_HOME=/tmp/mysqlsh-codex mysqlsh --sql \
  --host '<connection.host>' --port '<connection.port>' --user '<connection.username>' \
  --passwords-from-stdin \
  --execute "SELECT 1;"
```

mysql fallback:
```bash
MYSQL_PWD='<connection.password>' mysql \
  -h '<connection.host>' -P '<connection.port>' -u '<connection.username>' \
  -e "SELECT 1;"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tidbcloud) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
