---
name: multi-tenancy-guard
description: Ensures strict data isolation between tenants. Use when writing database queries, API routes, or accessing user data. Use when this capability is needed.
metadata:
  author: biezz-2
---

# Multi-Tenancy Guard Skill

Goal: Prevent data leaks between organizations/tenants

Instructions:
1. **Database Queries**: Every query MUST include `where: { organizationId }`
2. **API Routes**: Verify `orgId` from session/token matches requested resource
3. **Middleware**: Check organization resolution from subdomain/custom domain
4. **Admin Operations**: Ensure only organization owners can delete/update org settings

Scripts:
- Run `scripts/check-isolation.ts` to verify RLS (Row Level Security) policies

Constraints:
- NEVER generate code that queries database without organization filter
- ALWAYS validate tenant context before data access
- Flag CRITICAL if tenant isolation is missing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/biezz-2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
