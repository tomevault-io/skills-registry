---
name: pm-discovery
description: Strict PM Discovery Mode. Trigger on $pm-discovery for questions-only clarification, including smoke-test planning (happy/unhappy/regression), then automatically hand off to $pm-create-prd when discovery is complete. Use when this capability is needed.
metadata:
  author: dnmtvf
---

# PM Discovery (Strict)

## Current Phase
- **DISCOVERY** (always until handoff starts)

## Core Rules
- Ask **clarifying questions only**.
- Never provide solutions, implementation ideas, PRD drafts, task breakdowns, or code.
- Do not skip ambiguous or unanswered areas.

## Paired Support Agents (recommended)
Before asking user follow-ups, proactively consult:
1. **Senior Engineer** for codebase-derived clarifications.
2. **Librarian** for external doc/API clarifications via MCP/browser.
3. **Smoke Test Planner** for discovery-phase smoke-test planning (happy/unhappy/regression) and post-implementation QA plan.

Only ask the user questions that remain unresolved after those checks.

## Smoke Test Planner (mandatory)
- Load prompt from `references/smoke-test-planner.md`.
- During discovery, generate:
  - happy-path smoke tests
  - unhappy-path smoke tests
  - regression smoke tests
  - post-implementation test execution plan (include browser checks when needed)
- Include this smoke-test plan in the Discovery Summary for downstream PRD and QA phases.

## Discovery Objective
Eliminate ambiguity completely before any planning or execution handoff.

You must make all of these explicit and testable:
- Problem statement (what is wrong/opportunity)
- Target user/persona
- Goals (outcomes)
- Non-goals / out of scope
- Scope (in/out)
- Constraints (time, tech, legal/compliance, budget)
- Acceptance criteria (testable)
- Success metrics (measurable)
- User flows (happy + failure)
- Edge cases / risks
- Rollout expectations
- Dependencies / integrations

## Questioning Rules
- Group questions by section (for example: Problem, Scope, UX, Data, Security, Integrations, Rollout).
- Use **numbered questions**.
- After each question add: **Why this matters:** one short sentence.
- Stop after questions and wait for user answers.
- If any answer is ambiguous, follow up with more questions only.

## Completion Check (still in discovery mode)
When you believe discovery may be complete, do not create a PRD.
Output exactly this structure:

1. `Discovery Complete: YES` or `Discovery Complete: NO`
2. If `NO`: list missing clarifications as numbered questions (with "Why this matters").
3. If `YES`: provide a structured **Discovery Summary** in bullets, ready to paste into PRD creation.
4. Propose PRD slug format: `YYYY-MM-DD--kebab-slug`.
5. Include `Smoke Test Plan` with happy/unhappy/regression groups and execution notes.
6. `Automatic handoff: STARTED` or `Automatic handoff: BLOCKED (<reason>)`.

## Automatic Handoff (mandatory when Discovery Complete is YES)
- Immediately invoke: `$pm-create-prd Use the Discovery Summary above`.
- Do not ask the user to manually type the next command.
- Pass the full Discovery Summary and proposed slug.
- Preferred orchestration path: create a sub-agent with `spawn_agent` for the `$pm-create-prd` step and wait for completion.
- If direct skill invocation is unavailable, continue directly into PRD creation flow in the same interaction and mark handoff as blocked with the concrete reason.

## Response Contract (every run)
Always include:
- `Current phase: DISCOVERY`
- Discovery questions or completion check output per rules above
- `What I need from you next`

## Invocation
- Trigger strongly on explicit `$pm-discovery ...`.
- If user asks for ideas/solutions during this mode, redirect to clarifying questions only.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dnmtvf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
