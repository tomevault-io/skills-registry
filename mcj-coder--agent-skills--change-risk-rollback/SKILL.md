---
name: change-risk-rollback
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# Change Risk & Rollback Assessment

## Intent

Assess the operational risk of changes and ensure rollback options are explicit,
tested where possible, and aligned to traceability practices.

---

## When to Use

- Before releasing or deploying significant changes
- When touching critical paths, data handling, auth, or infrastructure
- When introducing migrations or behavioural changes with user impact

---

## Precondition Failure Signal

- No clear failure modes or rollback strategy exists
- Rollback depends on guesswork or mutable deployments
- Risk is described qualitatively without concrete triggers/mitigations
- Deployment plan lacks version/tag traceability

---

## Postcondition Success Signal

- Key risks and failure modes are identified and bounded
- Rollback options are explicit and traceable to versions/tags
- Mitigations are aligned to quality gates and environment traceability
- Decision to proceed is reviewable and justified

---

## Process

1. **Source Review**: Inspect the proposed changes, deployment environment, and current telemetry/monitoring coverage.
2. **Implementation**: Identify potential failure modes and define the corresponding rollback or remediation steps.
3. **Verification**: Conduct a walkthrough or simulation of the rollback plan to ensure it is executable and effective.
4. **Documentation**: Record the risk assessment and rollback plan in the change request or as an ADR for high-risk architectural changes.
5. **Review**: Release Manager/SRE and Tech Lead must validate the feasibility and completeness of the rollback strategy.

---

## Example Test / Validation

- For a change, identify at least one failure mode and define a rollback path
- Verify rollback candidate versions/tags can be identified from traceability records
- Confirm mitigations are in place for highest-likelihood/highest-impact failures

---

## Common Red Flags / Guardrail Violations

- “We can just hotfix it”
- Relying on manual steps without documentation
- Deploying without tags, making rollback ambiguous
- Treating rollback as “restore from backup” without feasibility validation

---

## Recommended Review Personas

- **Tech Lead** – validates risk framing and scope discipline
- **Platform/DevOps Engineer** – validates deployment/rollback feasibility
- **Domain Expert** – validates business/functional failure impacts
- **Security Reviewer** – validates integrity risks where applicable

---

## Skill Priority

P0 – Safety & Integrity

---

## Conflict Resolution Rules

- Overrides delivery pressure when rollback/risk is unclear
- Requires traceability (versions/tags); no rollback plan without provenance
- If risk cannot be bounded, escalate and defer release

---

## Conceptual Dependencies

- incremental-change-impact
- release-tagging-plan
- environment-traceability

---

## Classification

Core  
Governance

---

## Notes

Rollback is not a plan if it cannot be executed from recorded state.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
