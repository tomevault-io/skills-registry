---
name: eng-security-safety
description: Apply proactive threat modeling, least-privilege design, and safety guardrails before delivering any code or infrastructure change. Use when this capability is needed.
metadata:
  author: tjboudreaux
---

# Security and Safety Mindset

## Intent
- Treat every change as a potential attack surface or failure amplifier.
- Ensure data classification, secret handling, and permission scopes stay compliant.
- Bake safety checks (rate limits, input validation, monitoring) into the design, not after.

## Baseline Checklist
1. **Threat model quickly**: Who could abuse this surface? What capabilities do they need? What happens if they succeed?
2. **Data stewardship**: Classify data touched (PII, payments, assets) and enforce encryption, retention, and locality rules.
3. **Access + identity**: Validate authn/authz paths, key rotation, wallet signatures, and privilege escalation barriers.
4. **Dependency hygiene**: Pin versions, verify licenses, review changelogs, and prefer audited libraries/contracts.
5. **Secrets + config**: Never log secrets; store them in the project’s approved secret manager. Guard env var usage.

## Workflow
1. Enumerate entry points (mobile UI, API, smart contract, admin tools) and list unchecked inputs.
2. Define validation layers: schema-level, business-level, and environment-level (e.g., chain ID, platform version).
3. Ensure every state change is reversible or compensatable (feature flags, contract pausing, migration guards).
4. Instrument detection: structured logs, metrics, or on-chain events that can surface abuse or regressions fast.
5. Document explicit “never do” actions (e.g., disable signature checks, bypass paywalls) inside the PR/issue notes.

## Verification
- Run the project’s security/static analysis tooling (linters, contract analyzers, mobile scanners) and fix findings.
- Peer review the threat model summary; confirm secrets and keys are absent from diffs/logs.
- Validate abuse cases end-to-end (invalid payloads, replayed signatures, abusive traffic) before shipping.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tjboudreaux) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
