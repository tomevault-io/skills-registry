---
name: security-risk
description: Combine security scanning and threat modeling for changes involving data handling, API interception, sync, storage, authentication, and encryption. Use when this capability is needed.
metadata:
  author: laurenceputra
---

# Security Risk

Use this skill for security/privacy risk analysis and gate decisions.

## Scope
- Map trust boundaries and sensitive data flows.
- Review auth/session/token lifecycle and abuse controls.
- Produce severity-ranked findings with mitigations and residual risk.

## Role-Specific Guardrails
- Block critical exploitable findings.
- Require clear evidence for `APPROVE` decisions.
- Ensure optional sync/auth remains explicit and encrypted.

## Output
- Risk register with severity
- Required mitigations
- Gate Decision and Security Sign-off

## Canonical References
- Workflow gates: `docs/workflow/gates.md`
- Handoff contract: `docs/workflow/handoff-format.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/laurenceputra) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
