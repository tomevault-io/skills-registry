---
name: atomic-execution-engine
description: Convert high-level implementation plans into sequential, coherent PRs with one unit per PR. Use when executing a multi-step plan by scoping one unit at a time, creating a `codex/<unit-slug>` branch, opening a PR, iterating on Codex review feedback, and finalizing before starting the next unit. Do not batch unrelated changes. Use when this capability is needed.
metadata:
  author: cangiremir
---

# Plan-to-PR Iterative Executor

## Input
- A high-level implementation plan (bullets/numbered steps).

## Workflow (must follow in order)
1) Parse the plan into major, meaningful units of work.
2) For the current unit only:
   - Define scope, acceptance criteria, and a test plan.
   - Create a branch named `codex/<unit-slug>` (use `scripts/slugify_unit.py` if needed).
   - Implement the changes with clean, reviewable commits.
   - Open a PR for this unit with a clear description (scope, tests, risks).
3) Review cycle (mandatory):
   - Follow `references/review-cycle.md` for each review round.
   - Wait for Codex to review the PR.
   - For each suggestion:
     - If logically valid and improves the implementation, apply it and commit to the same PR.
     - If incorrect/irrelevant/unsound, ignore it and briefly justify if needed.
   - Wait for review again and repeat until Codex has no further suggestions.
4) Finalize:
   - Ensure CI/tests pass and the PR is coherent/testable.
   - Approve/finalize the PR.
5) Only then proceed to the next unit and repeat.

## Constraints
- One coherent unit per PR; no unrelated batching.
- Do not start the next unit until the current PR is finalized.
- Maintain clean commit history and clear PR descriptions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cangiremir) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
