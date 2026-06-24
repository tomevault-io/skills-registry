---
name: azure-sql-database
description: Azure SQL Database API skill. Use when working with Azure SQL Database for subscriptions. Covers 1 endpoint. Use when this capability is needed.
metadata:
  author: Lap-Platform
---

# Azure SQL Database
API version: 2014-04-01

## Auth
OAuth2

## Base URL
https://management.azure.com

## Setup
1. Configure auth: OAuth2
3. POST /subscriptions/{subscriptionId}/providers/Microsoft.Sql/checkNameAvailability -- create first checkNameAvailability

## Endpoints

1 endpoints across 1 groups. See references/api-spec.lap for full details.

### subscriptions
| Method | Path | Description |
|--------|------|-------------|
| POST | /subscriptions/{subscriptionId}/providers/Microsoft.Sql/checkNameAvailability | Determines whether a resource can be created with the specified name. |

## Common Questions

Match user requests to endpoints in references/api-spec.lap. Key patterns:
- "Create a checkNameAvailability?" -> POST /subscriptions/{subscriptionId}/providers/Microsoft.Sql/checkNameAvailability
- "How to authenticate?" -> See Auth section

## Response Tips
- Check response schemas in references/api-spec.lap for field details
- Create/update endpoints typically return the created/updated object

## References
- Full spec: See references/api-spec.lap for complete endpoint details, parameter tables, and response schemas

> Generated from the official API spec by [LAP](https://lap.sh)

---
> Source: [Lap-Platform/claude-marketplace](https://github.com/Lap-Platform/claude-marketplace) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
