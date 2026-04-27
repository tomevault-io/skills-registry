---
name: laravelpolicies-gates
description: Strengthen Laravel validation/auth/authorization boundaries with explicit failure-safe implementation patterns. Use for policies gates tasks. Use when this capability is needed.
metadata:
  author: makfly
---

# Policies Gates (Laravel)

## Use when
- Hardening authentication/authorization/validation paths.
- Standardizing access control and error semantics.

## Default workflow
1. Map actors, protected resources, and allowed actions.
2. Implement validation + authorization at explicit boundaries.
2. Apply throttling and consistent failure responses.
2. Test authorized/unauthorized/invalid scenarios.

## Guardrails
- Do not leak sensitive existence or permission details.
- Never rely on UI-only access checks.
- Keep auth and validation logic centralized.

## Progressive disclosure
- Start with this file for execution posture and constraints.
- Load references only for deep implementation detail or edge cases.

## Output contract
- Security boundary changes and rationale.
- Endpoints/middleware/policies updated.
- Negative-path test evidence.

## References
- `reference.md`
- `docs/complexity-tiers.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/makfly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
