---
name: branching-strategy-and-conventions
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# Branching Strategy and Conventions

## Intent

Define and enforce a simple, traceable branching model with conventional
commits, safe merges, and explicit handling for parallel work.

---

## When to Use

- Establishing or revising Git branching strategy.
- Enforcing commit message conventions.
- Coordinating parallel tasks on a shared feature branch.
- Defining merge policies for integration.

---

## Precondition Failure Signal

- Direct commits land on `main`.
- Merge commits are used for integration.
- Commit messages are non-conventional or verbose.
- Commit message rules are documented but not enforced by hooks or CI.
- Local hooks allow commits on the default branch.
- Feature branches lack traceability to tickets/issues when available.
- Parallel work on a feature branch is done without isolated task branches.

---

## Postcondition Success Signal

- All work lands via feature branches (no direct commits to `main`).
- Integration uses fast-forward or rebase-only merges (no merge commits).
- Conventional commits are enforced with concise titles and descriptions.
- Local hooks block commits to the default branch.
- Ticket/issue identifiers are included when available.
- Parallel tasks use transient task branches and merge back after verification.

---

## Process

1. **Source Review**: Inspect existing branching rules, hooks, and CI policies.
2. **Policy Definition**:
   - GitHub Flow: short-lived feature branches off `main`.
   - No direct commits to `main`.
   - Feature branches produce pre-release semantic versions.
   - Merge policy is `--ff-only` or rebase; no merge commits.
   - Parallel work uses transient task branches per worktree.
3. **Implementation**:
   - Configure a `commit-msg` hook and CI to block non-conventional commits.
   - Add a `pre-commit` hook to block commits on the default branch.
   - Enforce merge policy via branch protection or repo settings.
   - Document branch naming and ticket linkage expectations.
4. **Verification**: Demonstrate a conventional commit is accepted and a
   non-conventional commit is blocked.
5. **Documentation**: Update README or contributor docs with branch and commit
   rules.
6. **Review**: Tech Lead, Platform/DevOps, and Release Manager validate policy
   and traceability expectations.

---

## Example Test / Validation

- A feature branch merge succeeds only with rebase/fast-forward and a
  conventional commit message.
- A commit attempt on the default branch is blocked by the pre-commit hook.

---

## Common Red Flags / Guardrail Violations

- Pushing commits directly to `main` for "quick fixes".
- Allowing merge commits for convenience.
- Missing ticket identifiers when issues exist.
- Reusing a single branch for parallel tasks without isolation.
- Relying on documentation alone instead of automated enforcement.
- Bypassing local hooks to commit on the default branch.

---

## Recommended Review Personas

- **Tech Lead** - validates workflow simplicity and developer fit.
- **Platform/DevOps Engineer** - validates enforcement via tooling and CI.
- **Release Manager / SRE** - validates traceability and release impact.

---

## Skill Priority

P2 - Consistency & Governance

---

## Conflict Resolution Rules

- Traceability and quality gates override delivery speed.
- If merge policy conflicts with tooling, fix tooling rather than bypassing
  policy.

---

## Conceptual Dependencies

- automated-standards-enforcement
- quality-gate-enforcement
- finishing-a-development-branch
- semantic-version-impact

---

## Classification

Governance
Core

---

## Notes

Commit conventions:

- Use Conventional Commits with minimal, concise titles and descriptions.
- Include ticket/issue identifiers when available.
- Example: `feat(api): add paging support (ABC-123)`

Parallel work conventions:

- If multiple tasks are executed in parallel from one feature branch using
  worktrees, create a transient task branch per task.
- After verification, rebase or fast-forward the task branch back into the
  source feature branch.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
