---
name: finishing-a-development-branch
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# Finishing a Development Branch

## Intent

Close out work safely by verifying tests, presenting integration options, and
cleaning up worktrees consistently.

---

## When to Use

- After plan execution is complete.
- Before merging, creating a PR, or discarding work.

---

## Precondition Failure Signal

- Tests have not been run in the branch before integration.
- A merge or PR is created without verification evidence.
- Work is discarded without explicit confirmation.

---

## Postcondition Success Signal

- Tests pass with fresh evidence.
- One of the approved integration options is chosen.
- Worktrees are cleaned up appropriately.

---

## Process

1. **Verify Tests**: Run the project test suite and record results.
2. **Determine Base Branch**: Confirm merge base (usually `main`).
3. **Present Options**:
   - Merge locally.
   - Push and create PR.
   - Keep branch as-is.
   - Discard work (requires explicit confirmation).
4. **Execute Choice**: Follow the selected path precisely.
5. **Cleanup**: Remove worktree for options that discard or merge work.

---

## Example Test / Validation

- Tests pass on the branch before presenting options.

---

## Common Red Flags / Guardrail Violations

- Skipping verification because "it passed earlier".
- Automatically deleting worktrees without user confirmation.
- Forcing a merge without tests on the merged result.

---

## Recommended Review Personas

- **Tech Lead** - validates readiness for integration.
- **Release Manager / SRE** - validates release/traceability implications.

---

## Skill Priority

P2 - Consistency & Governance

---

## Conflict Resolution Rules

- If tests fail, return to `systematic-debugging`.
- Discarding work requires explicit user confirmation.

---

## Conceptual Dependencies

- verification-and-handover
- change-risk-rollback

---

## Classification

Governance  
Operational

---

## Notes

Branch closure is a release decision, not a formality.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
