---
name: local-dev-experience
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# Local Developer Experience Hardening

## Intent

Improve local feedback loops and enforce standards early to reduce CI churn and
prevent policy violations from reaching main. This includes ensuring that local
checks (git hooks) are efficient by only targeting staged files.

---

## When to Use

- Introducing or refining git hooks, local validation, or formatting.
- Reducing CI failures caused by avoidable local issues.
- Standardising developer workflows across teams.
- **Ensuring git hook performance** by implementing staged-only checks.

---

## Precondition Failure Signal

- Frequent CI failures due to basic style/lint/build issues.
- Developers regularly bypass local checks due to slowness.
- Git hooks run on the entire repository instead of only staged files.
- Inconsistent local formatting or tooling behaviour across machines.
- Hooks or local checks are unreliable or unclear.
- No local secret scanning is enforced before commits.

---

## Postcondition Success Signal

- Local checks fail fast and predictably on violations.
- Git hooks are scoped only to staged files (e.g., using `lint-staged`).
- Developers can run the same checks locally as CI expects (conceptually).
- Bypass paths are controlled and require explicit approval.
- Documentation explains the local workflow and expectations.
- Local checks enforce zero warnings and errors (see `quality-gate-enforcement`).
- Tool configuration is aligned across local and CI tools (see `automated-standards-enforcement`).
- Secret scanning runs locally and blocks commits when findings exist.

---

## Process

1. **Source Review**: Inspect the current local development setup (hooks, linters, etc.) and identify any gaps or inconsistencies.
2. **Implementation**: Configure or update the local development tools to enforce repository standards (e.g., husky hooks, linting rules).
   - **Efficiency**: Use tools like `lint-staged` to ensure that pre-commit hooks only run on the files being committed, preventing unnecessary whole-repo scans.
   - **Ordering**: Prefer a specialized tool if it can format; only add a general formatter (respecting `.editorconfig` or equivalent) when the specialized tool cannot, and run the specialized tool last.
   - **Consistency**: If checks depend on runtime versions, ensure repo pins are documented and align with `runtime-tooling-validation`.
   - **Secrets**: Add local secret scanning to hooks and ensure `.env` or similar files are excluded from version control.
3. **Verification**: Manually trigger a violation (e.g., bad formatting, lint error) in a staged file and verify that the local checks correctly identify and block it, while ignoring violations in unstaged files.
4. **Documentation**: Update the `README.md` or dedicated development docs with instructions on how to set up and use the tools.
5. **Review**: Tech Lead and Developer Representative review the setup for usability and effectiveness.

---

## Example Test / Validation

- Introduce a known lint/style violation and confirm local checks fail
- Confirm bypass mechanisms are not trivial or undocumented
- Confirm local checks align with quality-gate-enforcement expectations
- Confirm secret scanning blocks commits when seeded secrets are staged

---

## Common Red Flags / Guardrail Violations

- Allowing easy skipping of hooks without approval
- Making checks advisory because “developers complained”
- Divergence between local and CI expectations
- Global suppressions introduced to reduce local noise
- Skipping secret scanning in local hooks

---

## Recommended Review Personas

- **Tech Lead** – validates standards and adoption practicality
- **Platform Engineer** – validates consistency and reproducibility
- **Developer Representative** – validates clarity and usability without weakening policy

---

## Skill Priority

P3 – Delivery & Flow  
(Must not weaken P1/P0 standards.)

---

## Conflict Resolution Rules

- Cannot reduce standards; may improve ergonomics and speed
- If usability conflicts with enforcement, escalate rather than silently weaken gates
- Prefer fewer, reliable checks over many flaky ones (DRY/YAGNI)

---

## Conceptual Dependencies

- quality-gate-enforcement

---

## Classification

Operational

---

## Notes

Developer experience is leverage, but only if it enforces the right behaviour.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
