---
name: quality-gate-enforcement
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# Quality Gate Enforcement

## Intent

Ensure changes meet required quality standards (clean builds, lint/style,
analysis) without bypasses, suppressions, or silent degradations.
All verification outputs (builds, tests, linters, analyzers, security checks)
must be free of warnings and errors unless explicitly approved, including
Git and package-management warnings. Tooling configuration should be consistent
across the toolchain, and any warnings introduced by a config change must be
resolved before enforcement is considered complete.
Secret scanning results are part of the quality gate and must be clean.

---

## When to Use

- Before merging changes to main
- Before tagging or releasing any component
- Before deploying to any environment
- When introducing or modifying quality checks
- When standard/tooling configuration changes introduce new warnings

---

## Precondition Failure Signal

- Build warnings or errors occur
- Lint/style violations exist
- Analysis or security checks report findings
- Secret scanning reports findings
- Any verification step emits warnings (including non-blocking or advisory ones)
- Git or package-manager warnings remain unresolved after a configuration change
- Checks were bypassed or disabled for convenience

---

## Postcondition Success Signal

- All required checks pass
- All verification steps are clean (zero warnings and errors, including Git and
  package-management warnings)
- Secret scanning passes with zero findings (or explicitly approved exceptions)
- No global suppressions were introduced without explicit approval
- Evidence is reviewable and reproducible

---

## Process

1. **Source Review**: Inspect the current quality gate configuration and identify any active suppressions or bypassed checks.
2. **Implementation**: Fix violations or re-enable checks to ensure the quality gates are fully enforced.
3. **Alignment**: Confirm related tooling configuration is consistent (editor,
   formatter, VCS, package tooling). Resolve warnings introduced by the change.
4. **Verification**: Execute the quality gates and verify that they correctly identify and block failing code.
5. **Secrets Gate**: Ensure secret scanning runs in CI (and locally where configured) and blocks on findings.
6. **Documentation**: Record any unavoidable suppressions or threshold changes; use an ADR for any exception and obtain explicit user permission.
7. **Review**: Tech Lead and Security Reviewer review the enforcement status and any new suppressions.

---

## Example Test / Validation

- Run the required quality suite for impacted components and confirm pass
- Verify no new suppressions or bypass configurations were introduced
- Confirm failures are remediated rather than hidden
- Verify no Git or package-manager warnings remain after standards changes
- Verify secret scanning runs in CI and local workflows where configured

---

## Common Red Flags / Guardrail Violations

- Skipping hooks/checks "to save time"
- Globally disabling lint rules or warnings
- Marking checks "non-blocking" to get green
- Accepting new warnings as "known issues" without explicit user permission and ADR
- Leaving Git or package-manager warnings unresolved after a standards change
- Disabling or bypassing secret scanning to keep gates green

---

## Recommended Review Personas

- **Tech Lead** – validates standards adherence and no hidden degradations
- **Platform/DevOps Engineer** – validates checks match policy and are enforced
- **Security Reviewer** – validates security-related gate outcomes (where applicable)

---

## Skill Priority

P1 – Quality & Correctness  
(Escalate to P0 when security/integrity gates are involved.)

---

## Conflict Resolution Rules

- Overrides Delivery/Optimisation skills when they suggest bypasses
- Any suppression requires explicit escalation and documentation
- Scope of checks may be reduced via selective-build-test, but standards cannot

---

## Conceptual Dependencies

- incremental-change-impact
- selective-build-test (optional for scoping)

---

## Classification

Core  
Governance

---

## Notes

Green by suppression is a failure. Green by compliance is success.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
