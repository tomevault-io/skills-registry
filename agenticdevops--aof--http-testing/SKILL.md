---
name: http-testing
description: HTTP API testing and debugging Use when this capability is needed.
metadata:
  author: agenticdevops
---

# HTTP Testing Skill

Test HTTP APIs for functionality, performance, and debugging.

## When to Use This Skill

- Testing API endpoints
- Debugging HTTP issues
- Verifying authentication
- Checking response format
- Testing error conditions

## Steps

1. **GET request** — `curl http://endpoint/`
2. **POST with data** — `curl -X POST -d '{}' http://endpoint/`
3. **Check headers** — `curl -i http://endpoint/`
4. **Add auth** — `curl -H 'Authorization: Bearer token' http://endpoint/`
5. **Parse response** — `curl http://endpoint/ | jq .`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agenticdevops) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
