---
name: agentuity-cli-cloud-db-get
description: Show details about a specific database. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Db Get

Show details about a specific database

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud db get <name> [options]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<name>` | string | Yes | - |

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--showCredentials` | boolean | Yes | - | Show credentials in plain text (default: masked in terminal, unmasked in JSON) |
| `--showTables` | boolean | Yes | - | Fetch table schemas from the database |
| `--sql` | boolean | Yes | - | Output table schemas as SQL CREATE statements |

## Examples

Get database details:

```bash
bunx @agentuity/cli cloud db get my-database
```

Show database information:

```bash
bunx @agentuity/cli cloud db show my-database
```

Get database with credentials:

```bash
bunx @agentuity/cli cloud db get my-database --show-credentials
```

Get table schemas from the database:

```bash
bunx @agentuity/cli cloud db get my-database --show-tables
```

Get table schemas as SQL CREATE statements:

```bash
bunx @agentuity/cli cloud db get my-database --show-tables --sql
```

Get table schemas as JSON:

```bash
bunx @agentuity/cli cloud db get my-database --show-tables --json
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
