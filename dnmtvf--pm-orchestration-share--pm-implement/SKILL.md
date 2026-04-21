---
name: pm-implement
description: Execute approved Beads tasks, run automated post-implementation reviews, complete review iteration, then run Manual QA smoke tests and return for final user review. Use when this capability is needed.
metadata:
  author: dnmtvf
---

# PM Implement (Strict)

## Current Phase
- **IMPLEMENTATION** (then **POST-IMPLEMENTATION REVIEWS**, **REVIEW ITERATION**, **MANUAL QA SMOKE TESTS**, and **AWAITING FINAL REVIEW**)

## Preconditions (hard gate)
Before implementation starts, verify:
1. PRD is approved.
2. Beads task graph is approved.
3. Epic ID is known and exists.

If any precondition fails:
- Stop and ask only for missing prerequisite(s).

## Implementation Rules
- Keep paired support agents available:
  - **Senior Engineer** for proactive code-level guidance and risk checks.
  - **Librarian** for external docs/compatibility checks when implementation touches APIs/platform specifics.
- Execute tasks from ready queue first:
  - `bd ready --parent <epic-id> --pretty`
- Claim/start work:
  - `bd update <task-id> --claim`
- Keep changes scoped to active tasks.
- Log meaningful status notes:
  - `bd comments add <task-id> "<progress/update>"`
- Close completed tasks:
  - `bd close <task-id>`

## Automatic Dual-Agent Post-Implementation Review
After implementation tasks are complete, automatically run both reviewers:

1. **AGENTS Compliance Reviewer**
   - Load prompt from `references/agents-compliance.md`.
   - Check implementation against repo `AGENTS.md` rules and workflow constraints.
   - Return violations, file references, severity, and required fix.

2. **Jazz**
   - Load prompt from `references/jazz.md`.
   - Persona: grumpy, nitpicky old fart.
   - Behavior: doubt assumptions, challenge weak logic, call out edge cases and missing rigor.
   - Return concrete defects and demanded fixes.

Run both reviewers in parallel when possible.

Preferred orchestration calls:
- Spawn compliance reviewer agent from `references/agents-compliance.md`.
- Spawn `Jazz` reviewer agent from `references/jazz.md`.
- Wait for both to complete before creating iteration tasks.

## Review Output Handling (mandatory)
For every actionable review finding:
- Add comment on mapped task:
  - `bd comments add <task-id> "<reviewer>: <finding>"`
- Create iteration task when code changes are required:
  - `bd create --type task --parent <epic-id> --title "Review iteration: <short title>" --description "<fix + DoD>" --labels review,iteration`
- Set dependencies so unresolved findings block completion:
  - `bd dep <blocking-task-id> --blocks <blocked-task-id>`

## Review Iteration Loop
- Implement all review iteration tasks.
- Re-run targeted validation for changed areas.
- Close iteration tasks only when their DoD is met.

## Manual QA Smoke Tests (mandatory after automated reviews)
- After automated reviews and review-iteration fixes, run Manual QA smoke execution:
  - load prompt from `references/manual-qa-smoke.md`
  - execute discovery/PRD smoke tests for happy, unhappy, and regression coverage
  - run browser-based smoke checks when required by the test plan
- Preferred orchestration call:
  - spawn Manual QA agent and wait for completion before final handoff
- If smoke tests fail:
  - create/fill beads follow-up tasks with explicit DoD
  - implement fixes
  - rerun Manual QA smoke tests
- Continue until smoke tests pass or only user-accepted risks remain.

## Final Gate
- Move to `Current phase: AWAITING FINAL REVIEW`.
- Present:
  - implementation summary
  - reviewer findings summary
  - review-iteration changes completed
  - Manual QA smoke-test results
- Ask user to review results.
- Do not auto-finish beyond this gate.

## Output Requirements (every run)
Always include:
1. `Current phase: <...>`
2. `Epic ID`
3. `Active/ready tasks`
4. `Reviewer status` (not started/running/completed)
5. `Iteration task status`
6. `Manual QA smoke status` (not started/running/passed/failed)
7. `What I need from you next`

## Invocation
- Trigger strongly on `$pm-implement ...`.
- Also trigger as automatic handoff from `$pm-beads-plan` after second approval.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dnmtvf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
