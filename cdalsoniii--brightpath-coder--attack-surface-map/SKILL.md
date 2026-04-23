---
name: attack-surface-map
description: Map the API attack surface — enumerate all endpoints, authentication requirements, input validation, rate limits, and data sensitivity classification Use when this capability is needed.
metadata:
  author: cdalsoniii
---

# Attack Surface Map Skill

Build a comprehensive map of the API's attack surface by enumerating all entry points and their security controls.

## Trigger Conditions
- New endpoints are added
- Security review is conducted
- Threat model is being developed
- User invokes with "attack surface" or "attack-surface-map"

## Input Contract
- **Required:** Path to route configuration and handler files
- **Optional:** OpenAPI spec for validation

## Output Contract
- Complete endpoint inventory with HTTP method, path, and handler
- Per-endpoint security profile: auth required, scopes, rate limits, ownership check
- Input surface: all parameters, headers, and body fields that accept user input
- Data sensitivity classification: public / internal / confidential / restricted
- Risk heat map: high-risk endpoints flagged for deeper review

## Tool Permissions
- **Read:** Route config, handlers, middleware, DTOs, OpenAPI spec
- **Write:** Attack surface report
- **Search:** Grep for route registrations, input binding, validation tags

## Execution Steps

1. **Enumerate endpoints**: Extract all routes from main.go or router files
2. **Map auth controls**: For each endpoint, identify auth middleware, scope requirements, ownership checks
3. **Map input surface**: For each endpoint, list all inputs (path params, query params, headers, body fields)
4. **Classify data sensitivity**: Based on data touched (financial = restricted, PII = confidential, etc.)
5. **Map rate limits**: Identify rate limiting configuration per endpoint
6. **Assess risk**: Score each endpoint based on: data sensitivity × input complexity × auth strength
7. **Report**: Produce attack surface map with risk heat map

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cdalsoniii) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
