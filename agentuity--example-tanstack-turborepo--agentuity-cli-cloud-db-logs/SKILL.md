---
name: agentuity-cli-cloud-db-logs
description: Get query logs for a specific database. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Db Logs

Get query logs for a specific database

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud db logs <database> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<database>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--startDate` | string | Yes | - | Start date for filtering logs |
| `--endDate` | string | Yes | - | End date for filtering logs |
| `--username` | string | Yes | - | Filter by username |
| `--command` | string | Yes | - | Filter by SQL command type |
| `--hasError` | boolean | Yes | - | Show only queries with errors |
| `--sessionId` | string | Yes | - | Filter by session ID (trace ID) |
| `--showSessionId` | boolean | No | `false` | Show session ID column in output |
| `--showUsername` | boolean | No | `false` | Show username column in output |
| `--pretty` | boolean | No | `false` | Show full formatted SQL on separate line |
| `--limit` | number | No | `100` | Maximum number of logs to return |
| `--timestamps` | boolean | No | `true` | Show timestamps in output |

## Examples

View query logs for database:

```bash
bunx @agentuity/cli cloud db logs my-database
```

Limit to 50 log entries:

```bash
bunx @agentuity/cli cloud db logs my-database --limit=50
```

Show only queries with errors:

```bash
bunx @agentuity/cli cloud db logs my-database --has-error
```

Filter by username:

```bash
bunx @agentuity/cli cloud db logs my-database --username=user123
```

Filter by SQL command type:

```bash
bunx @agentuity/cli cloud db logs my-database --command=SELECT
```

Filter by session ID:

```bash
bunx @agentuity/cli cloud db logs my-database --session-id=sess_abc123
```

Show session ID column:

```bash
bunx @agentuity/cli cloud db logs my-database --show-session-id
```

Show username column:

```bash
bunx @agentuity/cli cloud db logs my-database --show-username
```

Show full formatted SQL on separate lines:

```bash
bunx @agentuity/cli cloud db logs my-database --pretty
```

## Output

Returns: `array`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
