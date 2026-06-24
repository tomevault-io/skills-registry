---
name: azure-sql-database-datamasking-policies-and-rules
description: Azure SQL Database Datamasking Policies and Rules API skill. Use when working with Azure SQL Database Datamasking Policies and Rules for subscriptions. Covers 4 endpoints. Use when this capability is needed.
metadata:
  author: Lap-Platform
---

# Azure SQL Database Datamasking Policies and Rules
API version: 2014-04-01

## Auth
OAuth2

## Base URL
https://management.azure.com

## Setup
1. Configure auth: OAuth2
2. GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}/dataMaskingPolicies/{dataMaskingPolicyName}/rules -- verify access

## Endpoints

4 endpoints across 1 groups. See references/api-spec.lap for full details.

### subscriptions
| Method | Path | Description |
|--------|------|-------------|
| PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}/dataMaskingPolicies/{dataMaskingPolicyName} | Creates or updates a database data masking policy |
| GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}/dataMaskingPolicies/{dataMaskingPolicyName} | Gets a database data masking policy. |
| PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}/dataMaskingPolicies/{dataMaskingPolicyName}/rules/{dataMaskingRuleName} | Creates or updates a database data masking rule. |
| GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}/dataMaskingPolicies/{dataMaskingPolicyName}/rules | Gets a list of database data masking rules. |

## Common Questions

Match user requests to endpoints in references/api-spec.lap. Key patterns:
- "Update a dataMaskingPolicy?" -> PUT /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}/dataMaskingPolicies/{dataMaskingPolicyName}
- "Get dataMaskingPolicy details?" -> GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}/dataMaskingPolicies/{dataMaskingPolicyName}
- "Update a rule?" -> PUT /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}/dataMaskingPolicies/{dataMaskingPolicyName}/rules/{dataMaskingRuleName}
- "List all rules?" -> GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}/dataMaskingPolicies/{dataMaskingPolicyName}/rules
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
