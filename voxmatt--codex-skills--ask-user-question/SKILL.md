---
name: ask-user-question
description: Run a structured requirements interview to gather missing decisions, constraints, risks, and tradeoffs one question at a time using an ask-user tool pattern. Use when the user asks to be interviewed, when requirements/specs/plans are ambiguous, or when Codex needs guided, option-based questioning before planning or implementation. Use when this capability is needed.
metadata:
  author: voxmatt
---

# Ask User Question

## Overview

Run a discovery-driven interview that asks high-value, non-obvious questions and waits for answers before moving forward.

## Interview Workflow

1. Explore before asking.
- Inspect the artifact and surrounding context first (codebase, spec, constraints, prior decisions).
- Derive likely decision points from real context; do not ask blind questions.
2. Identify critical decisions.
- Prioritize choices that impact architecture, scope, compliance, or rework risk.
- Delay low-impact implementation details until direction is clear.
3. Design structured questions.
- Ask one question at a time by default.
- Provide 2-4 mutually exclusive options with tradeoffs.
- Put the recommended option first and label it `(Recommended)`.
4. Ask and wait.
- Use the ask-user tool when available.
- Do not continue implementation/planning until the user answers, defers, or declines.
5. Iterate until complete.
- Convert each answer into explicit decisions/assumptions.
- Ask the next highest-risk question until completion criteria pass.

## Question Design Rules

- Keep headers short (12 characters or fewer).
- Ground every question in discovered context and unresolved risk.
- Make options concrete and decision-ready, not vague.
- Include one sentence per option explaining impact/tradeoff.
- Prefer single-question turns; batch only when latency matters and questions are independent.
- Challenge ambiguous terms with measurable prompts (for example: p95 latency, max steps, error budget).

## Ask-User Tool Contract

When a dedicated question tool is available, use this shape:

- `question`: direct question with minimal context
- `header`: short tag (max 12 chars)
- `options`: 2-4 exclusive choices
- Each option has:
- `label`: short choice name
- `description`: one-sentence impact/tradeoff
- `multiSelect`: `false` unless multiple answers can legitimately apply

If the tool is unavailable, present the same structure in markdown and ask the user to reply with one option label or `Other` plus details.

## Coverage Areas

Ensure each area is either decided or explicitly deferred with reason:

- Product intent and measurable success
- Primary users and critical journeys
- Scope boundaries and phased delivery
- Architecture and dependency strategy
- Data model, privacy, security, and compliance
- Reliability, failure behavior, and rollback
- Performance, scale assumptions, and limits
- UX states, accessibility, and edge-case behavior
- Testing strategy and acceptance criteria
- Ownership, operations, and maintenance model
- Tradeoffs, rejected alternatives, and rationale

## Interview State Tracking

After each answer, maintain:

- `Decided`: decisions the user committed to
- `Assumed`: temporary assumptions pending confirmation
- `Open`: unresolved questions ordered by risk
- `Next`: next question candidate with reason

## Completion Criteria

Stop interviewing only when all are true:

- No high-risk open decisions remain.
- All coverage areas are decided or deferred with rationale.
- Remaining unknowns are low risk and time-bounded.
- A builder can proceed without guessing core behavior.

## Output Contract

Return:

- Interview summary
- Decisions captured
- Assumptions still active
- Open questions (if any)
- Recommended next question (if not complete)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/voxmatt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
