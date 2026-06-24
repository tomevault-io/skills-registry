---
name: cockpit-planning
description: Planning-only skill: produce a dependency-aware plan and acceptance criteria; do not commit. Use when this capability is needed.
metadata:
  author: future3ooo
---

# Cockpit Planning

You are producing a plan for an Agentic Cockpit task.

## Rules
- Planning tasks must not make commits.
- Be explicit about acceptance criteria, sequencing, and validation commands.
- Keep the plan small and testable (few steps, clear checkpoints).
- If the plan will lead to `EXECUTE` work, include a “Git Contract” note (base sha + work branch naming) so the controller can dispatch deterministically.
- Apply the authoritative shared doctrine in `AGENTS.md` during planning.
- Local consequence: if the plan shapes cockpit runtime code, worker behavior, routing, cleanup, or contracts, apply `Mandatory Skill Invocation (Fail-Closed)` during planning and include the executor's exact verification, quality-gate, and coupled-update commands in the plan.
- This applies both in explicit planning mode and when you are planning inside an execution turn before editing code.
- For preflight-required code turns, the plan must mirror the writer preflight contract:
  - `goal`
  - `reusePath`
  - `modularityPlan`
  - `chosenApproach`
  - `rejectedApproaches`
  - `touchpoints`
  - `coupledSurfaces`
  - `riskChecks`
  - `openQuestions`

## Output contract
Return **only** JSON matching the worker output schema.
- Put the plan in `planMarkdown` as a numbered list.
- Leave `commitSha` empty.
- Populate `testsToRun` with the commands the executor should run.
- If runtime code is in scope, the plan must include the specific verification and quality-gate commands the executor will run.

## Learned heuristics (SkillOps)
<!-- SKILLOPS:LEARNED:BEGIN -->
<!-- SKILLOPS:LEARNED:END -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/future3ooo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
