---
name: verify-persistence
description: > Use when this capability is needed.
metadata:
  author: hmislk
---

# Verify Persistence Configuration

Check `src/main/resources/META-INF/persistence.xml` for deployment readiness.

## Steps

1. **Read persistence.xml** at `src/main/resources/META-INF/persistence.xml`

2. **Check JNDI datasources** - Must use environment variables:
   - `${JDBC_DATASOURCE}` (not `jdbc/coop`, `jdbc/rhDS`, etc.)
   - `${JDBC_AUDIT_DATASOURCE}` (not `jdbc/ruhunuAudit`, etc.)

3. **Check DDL generation paths** - Must NOT contain hardcoded paths:
   - No `eclipselink.application-location` with `c:/tmp/` or `/home/*/tmp/`

4. **Report findings** clearly:
   - If all correct: "Persistence.xml is deployment-ready"
   - If issues found: List each issue with the current value and what it should be

## What's Correct vs Wrong

| Setting | Correct | Wrong |
|---------|---------|-------|
| Main datasource | `${JDBC_DATASOURCE}` | `jdbc/coop`, `jdbc/rhDS` |
| Audit datasource | `${JDBC_AUDIT_DATASOURCE}` | `jdbc/ruhunuAudit` |
| DDL location | Not present or env var | `c:/tmp/`, `/home/buddhika/tmp/` |

## If Issues Found

Offer to fix by replacing hardcoded values with environment variables. Do NOT auto-fix without user confirmation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hmislk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
