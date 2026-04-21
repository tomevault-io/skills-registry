---
name: sf-cli-execution
description: Salesforce CLI (sf) usage for running SOQL queries, authentication, and handling output. Use when executing queries or managing Salesforce connections. Use when this capability is needed.
metadata:
  author: grailautomation
---

# Salesforce CLI Execution Guide

This skill covers using the `sf` CLI to run SOQL queries against Salesforce orgs.

## Prerequisites

- Salesforce CLI installed: `npm install -g @salesforce/cli`
- At least one authenticated org

## Quick Reference

| Command | Purpose |
|---------|---------|
| `sf org list` | List connected orgs |
| `sf org login web` | Authenticate new org |
| `sf data query` | Run SOQL query |
| `sf data export bulk` | Export large datasets |
| `sf sobject describe` | Get object schema |

## Running Queries

### Basic Query

```bash
sf data query --query "SELECT Id, Name FROM Account LIMIT 10" --target-org production
```

### With JSON Output (Recommended)

```bash
sf data query --query "SELECT Id, Name FROM Account" --target-org production --json
```

### Limit Output Size

```bash
sf data query --query "SELECT Id, Name FROM Account" --target-org production --json 2>&1 | head -100
```

## Detailed References

- [Authentication](authentication.md) - Login, org management, aliases
- [Output Formats](output-formats.md) - JSON, CSV, table formats
- [Error Handling](error-handling.md) - Common errors and solutions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/grailautomation) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
