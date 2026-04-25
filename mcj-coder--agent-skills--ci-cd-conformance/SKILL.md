---
name: ci-cd-conformance
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# CI/CD Pipeline Conformance Review

## Intent

Ensure CI/CD pipelines enforce the repository’s operating contract: quality,
traceability, incremental execution, and immutability.

---

## When to Use

- Creating new pipelines for a component
- Modifying build/test/release/deploy pipelines
- Reviewing pipeline drift or exceptions

---

## Precondition Failure Signal

- Pipelines rebuild/retest/redeploy everything without justification
- Pipelines allow deployment from mutable refs (branches)
- Required quality checks are missing or non-blocking
- Tagging/versioning is inconsistent or absent

---

## Postcondition Success Signal

- Pipelines run the right scope based on impact
- Quality gates are enforced and reproducible
- Releases are immutable and tag-linked
- Deployments reference explicit versions/tags
- Exceptions are explicit and reviewed

---

## Process

1. **Source Review**: Audit the pipeline configuration (YAML, scripts, etc.) and compare against monorepo standards.
2. **Implementation**: Adjust the pipeline to ensure it enforces quality gates, uses tag-based deployments, and implements incremental execution.
3. **Verification**: Execute the pipeline and verify that it correctly handles both valid and invalid changes (e.g., fails on quality gate violations).
4. **Documentation**: Document any exceptions or specific pipeline behaviors that differ from the standard.
5. **Review**: Platform/DevOps Engineer and Tech Lead review the pipeline for conformance and security.

---

## Example Test / Validation

- Demonstrate a change triggers only impacted pipelines
- Demonstrate pipeline fails on quality gate violations
- Demonstrate deployment input is an immutable version/tag reference

---

## Common Red Flags / Guardrail Violations

- Making checks advisory to reduce failures
- Introducing pipeline exceptions without documentation
- Deploying from main because “it’s simpler”
- Skipping traceability mechanisms for non-prod environments

---

## Recommended Review Personas

- **Platform/DevOps Engineer** – validates pipeline behaviour and enforcement
- **Tech Lead** – validates alignment to operating contract and scope discipline
- **Security Reviewer** – validates provenance and integrity controls

---

## Skill Priority

P2 – Consistency & Governance  
(Escalate to P0 when traceability/integrity controls are affected.)

---

## Conflict Resolution Rules

- Pipelines must not weaken P0/P1 constraints for convenience
- Incremental behaviour must be grounded in impact analysis
- If requirements conflict, default to traceability and safety

---

## Conceptual Dependencies

- incremental-change-impact
- release-tagging-plan
- quality-gate-enforcement

---

## Classification

Governance  
Operational

---

## Notes

Pipelines are policy enforcement. Treat them as production code.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
