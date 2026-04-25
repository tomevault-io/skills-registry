---
name: incremental-change-impact
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# Incremental Change Impact Analysis

## Intent

Determine the precise scope and consequences of a change before build, test,
refactor, versioning, or deployment activity occurs.

This skill exists to prevent unnecessary work, reduce brownfield risk, enable
incremental delivery in a monorepo, and provide objective inputs to versioning
and release decisions.

---

## When to Use

- Any time a change set exists (code/config/infra/docs)
- Before planning builds/tests/deployments
- Before deciding semantic version impact
- Before refactors that may cross boundaries

---

## Precondition Failure Signal

- Full builds/tests run regardless of scope
- Deployment scope is unclear or overly broad
- Version bumps are guessed rather than derived
- Unrelated components are impacted unexpectedly
- Reviewers cannot answer “what does this change affect?”
- Risk assessment is subjective or undocumented

---

## Postcondition Success Signal

- Affected components/slices/libraries are explicitly listed
- Unaffected components are explicitly listed
- Required builds/tests/deployments are enumerated and justified
- Downstream and transitive impacts are identified
- Change scope is bounded, reproducible, and reviewable

---

## Process

1. **Source Review**: Analyze the diff of the changes and identify the directly modified files and their associated components.
2. **Implementation**: Trace dependencies from modified files to identify transitively impacted components.
3. **Verification**: List all impacted and unimpacted components and justify why each is (or is not) affected.
4. **Documentation**: Document the impact set for subsequent build, test, and release planning.
5. **Review**: Tech Lead and Platform/DevOps Engineer must review the impact set for accuracy and completeness.

---

## Example Test / Validation

- Given a diff range, produce an impact list and verify only impacted components run
- Verify unrelated components are not built/tested/deployed
- Confirm versioning decisions reference the impacted set
- A second reviewer can reproduce the same impact set from the same inputs

---

## Common Red Flags / Guardrail Violations

- “It’s safer to just rebuild everything”
- Running full pipelines to avoid impact reasoning
- Assuming changes are local without checking dependencies
- Broadening scope “just in case” without evidence
- Version bumps without a clear impact narrative

---

## Recommended Review Personas

- **Tech Lead** – validates scope correctness and downstream implications
- **Platform/DevOps Engineer** – validates build/test/deploy scope alignment
- **Domain Expert** – validates behavioural dependency assumptions

---

## Skill Priority

P1 – Quality & Correctness  
(Escalate to P0 where incorrect impact could cause unsafe deployments or integrity breaches.)

---

## Conflict Resolution Rules

- Must precede any skill that builds/tests/deploys/versions/refactors
- Overrides Delivery/Optimisation skills when they skip impact reasoning
- If impact cannot be determined confidently, escalate before proceeding
- Downstream skills must not broaden scope beyond this output without justification

---

## Conceptual Dependencies

None

---

## Classification

Core  
Governance

---

## Notes

This is foundational. It replaces intuition with explicit, reviewable scope.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
