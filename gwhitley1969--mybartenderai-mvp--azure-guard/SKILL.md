---
name: azure-guard
description: Use this skill to review code for security violations, architectural drifts, or Azure Functions v4 incompatibilities before finalizing any task.
metadata:
  author: gwhitley1969
---

# Azure Guardian

## Purpose

To enforce the "Zero Trust" and "Production Ready" standards of the MyBartenderAI backend.

## Inspection Checklist

1. **No Connection Strings:** Verify that Storage and Database connections use Managed Identity or Key Vault references (`@Microsoft.KeyVault`).
2. **V4 Logging Compliance:** Scan for `context.log.error` (BANNED) and replace with `context.error`.
3. **User Context:** Verify that API handlers extract user info using the header pattern:
   ```javascript
   const userId = request.headers.get('x-user-id') || decodeJwtClaims(request.headers.get('authorization'))?.sub;

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gwhitley1969) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
