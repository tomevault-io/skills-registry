---
name: monorepo-orchestration-setup
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# Monorepo Orchestration Setup

## Intent

Enable affected-only builds and tests in a monorepo using an orchestration tool
that coordinates existing language-specific tooling without replacing it.

---

## When to Use

- Introducing a monorepo orchestration tool.
- Migrating a monorepo to affected-only execution.
- Refactoring CI or hooks to avoid full-repo runs.

---

## Precondition Failure Signal

- Every commit or PR runs full build/test across the repo.
- Monorepo orchestration is absent or inconsistently configured.
- Orchestrator replaces language tooling instead of coordinating it.

---

## Postcondition Success Signal

- Affected-only commands are configured and documented.
- CI and hooks use affected-only execution where appropriate.
- Orchestration works across supported components without warnings.

---

## Process

1. **Source Review**: Inspect repo structure, CI, and existing tooling.
2. **Selection**: Compare orchestration options and obtain explicit user
   approval for the chosen tool.
3. **Implementation**: Configure affected-only commands and integrate with
   existing build/test scripts.
4. **Verification**: Prove affected-only execution works and does not skip
   required quality gates.
5. **CI Alignment**: Update pipeline and hooks to use affected-only execution
   where appropriate (see `ci-cd-conformance`).
6. **Documentation**: Update README and ADRs for the orchestration decision.
7. **Review**: Platform/DevOps and Tech Lead validate CI alignment.

---

## Example Test / Validation

- Affected-only command runs only impacted components with passing gates.

---

## Common Red Flags / Guardrail Violations

- Switching orchestration tools without a migration plan.
- Disabling quality gates to make affected-only execution "green".
- Orchestrator-specific config diverges from repo standards.

---

## Recommended Review Personas

- **Platform/DevOps Engineer** - validates CI/CD integration and performance.
- **Tech Lead** - validates scope control and quality alignment.

---

## Skill Priority

P2 - Consistency & Governance

---

## Conflict Resolution Rules

- `quality-gate-enforcement` overrides speed optimizations.
- If affected-only selection is ambiguous, `incremental-change-impact` wins.

---

## Conceptual Dependencies

- incremental-change-impact
- selective-build-test
- ci-cd-conformance
- quality-gate-enforcement

---

## Classification

Governance
Operational

---

## Notes

Orchestration coordinates existing tooling; it does not replace language-specific
build/test tools.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
