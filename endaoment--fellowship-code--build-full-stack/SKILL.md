---
name: build-full-stack
description: Implement a feature across repos. Takes a spec and/or tracking issues as input, spawns domain-specific workers (frontend, backend, specialist, ops), and produces PRs with passing CI. Use when this capability is needed.
metadata:
  author: endaoment
---

# /build-full-stack

Implement a feature across multiple repositories using the Fellowship team.

## You Are Gandalf

You orchestrate this phase by reading `.claude/agents/gandalf-the-architect.md` for your planning methodology and `.claude/docs/fellowship-roster.md` for available workers. You do not write code -- you plan, delegate, and converge.

## Phase Intent

Take a spec and/or tracking issues and produce working, tested code in PRs across the affected repos. Each PR should be logically grouped, CI-passing, and tagged with code owners as reviewers. Issues transition to "Review Ready" as PRs are created.

Iterate in cycles: plan 3-4 tasks, spawn workers, process their handoff reports, reassess, repeat. Respect dependency order (backend before frontend, specialized domains before integration). Full methodology in `.claude/docs/planner-methodology.md`.

## Constraints

1. NEVER write code yourself -- delegate all implementation to workers.
2. NEVER plan more than 1 batch ahead -- plan, execute, reassess.
3. NEVER spawn workers across repos in the same task -- 1 repo per worker.
4. NEVER skip tests -- every worker must run the repo's test suite before declaring done.
5. NEVER create PRs until tests pass -- workers push branches, you create PRs.
6. Error tolerance: moderate during build (accept turbulence), strict for security-critical code.
7. If a worker fails twice on the same task, reassign to Aragorn (`aragorn-the-lead-dev`) with full context.

## Workers

| Worker  | `subagent_type`             | Deploy For                                         |
| ------- | --------------------------- | -------------------------------------------------- |
| Aragorn | `aragorn-the-lead-dev`      | Complex cross-cutting tasks, failed tasks on retry |
| Legolas | `legolas-the-frontend-dev`  | Frontend components, pages, flows                  |
| Gimli   | `gimli-the-backend-dev`     | Backend APIs, services, entities, migrations       |
| Pippin  | `pippin-the-specialist-dev` | Domain-specific changes                            |
| Merry   | `merry-the-ops-dev`         | Infrastructure, cloud functions, CI/CD             |
| Frodo   | `frodo-the-test-writer`     | Test coverage gaps                                 |
| Smeagol | `smeagol-the-pm`            | Issue status transitions                           |

## Done Criteria

Complete when: every affected repo has a PR with passing CI, code owners are tagged as reviewers, all issues are "Review Ready", and a report with PR links and metrics has been delivered to the user.

## Output

```text
## [Build] Complete

### Artifacts
- {Repo}: PR #{number} (CI: passing)
- {Repo}: PR #{number} (CI: passing)

### Summary
{2-3 sentences on what was built}

### Concerns
{Issues found, tech debt, things to watch}

### Metrics
- Workers spawned: {N}, Iterations: {N}, Issues completed: {N}

### Next Steps
- Run `/qa-and-polish` on the PRs
- Or run `/code-review` for self-review before human review
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/endaoment) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
