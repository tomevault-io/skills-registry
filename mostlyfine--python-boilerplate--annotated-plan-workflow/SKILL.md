---
name: annotated-plan-workflow
description: Structured workflow that separates research, planning, annotation, and implementation. Use when tackling non-trivial coding tasks that require architecture control, reduced rework, and explicit user sign-off before writing code. Use when this capability is needed.
metadata:
  author: mostlyfine
---

# Annotated Plan Workflow

Drive Agent work through a strict phase pipeline: Research → Plan → Annotate → Todo → Implement → Feedback.

## Core Rule

Never write or modify implementation code until a written plan has been reviewed and explicitly approved.

## Phase 1: Research

1. Read relevant folders and flows deeply.
2. Produce a persistent `research.md` with findings, assumptions, risks, and open questions.
3. Review `research.md` before moving forward.
4. Correct misunderstandings in the document, then refresh it.

Use prompts like:

```
- Read this area in depth and capture all relevant behavior, constraints, and edge cases in `research.md`.
- Study this subsystem deeply, identify potential bugs, and keep researching until root causes are listed in `research.md`.
```

## Phase 2: Plan

1. Create a persistent `plan.md` based on actual source files.
2. Include approach, affected files, code-level change strategy, and trade-offs.
3. Prefer concrete references when available (existing internal or OSS implementation).
4. Keep implementation blocked until explicit approval.

Require plan contents:

- Goal and non-goals
- Files and interfaces to change
- Data flow and failure handling
- Type-safety and validation strategy
- Migration/compatibility impact (if relevant)
- Rollback strategy (if relevant)

## Annotation Cycle (1-6 rounds)

1. Review `plan.md` in deep think.
2. Add inline notes to reject, constrain, or refine decisions.
3. Ask Agent to address all notes and update `plan.md`.
4. Repeat until the plan matches product and architecture intent.
5. Keep the guardrail explicit: do not implement yet.

Use prompts like:

```
- I added inline notes. Address all notes and update `plan.md`. Do not implement yet.
- Remove optionality here, keep this API shape unchanged, and simplify this section.
```

## Todo Breakdown

Before coding, add a granular checklist into `plan.md`.

Rules:

- Break work into phases and concrete tasks.
- Mark tasks complete as implementation progresses.
- Keep this checklist as the single progress source of truth.

## Phase 3: Implementation

After approval, execute the entire plan without stopping for unnecessary confirmations.

Execution contract:

1. Implement all approved tasks.
2. Mark each finished task in `plan.md`.
3. Avoid unnecessary comments and avoid weak types.
4. Run typecheck/test continuously to catch issues early.
5. Stop only when all tasks are complete or a real blocker appears.

Use prompt template:

```
- Implement the approved `plan.md` fully.
- When a task or phase is complete, mark it complete in `plan.md`.
- Do not stop until all approved tasks are complete.
- Continuously run typecheck/tests and fix introduced issues.
```

## Feedback During Execution

Keep feedback terse and directive.

Examples:

- You missed task X from `plan.md`.
- Move this change to module Y.
- Match table layout with existing page Z.
- Revert this direction. Scope is now only A.

Prefer referencing existing project patterns over describing UI/architecture from scratch.

## Steering Rules

During planning and implementation:

1. Accept only changes aligned with current priorities.
2. Trim nice-to-have scope aggressively.
3. Protect stable public interfaces unless explicitly approved.
4. Override tool choices when project conventions require it.

## Session Strategy

Prefer one long session for research + planning + implementation so context compounds.

Persistence anchors:

- `research.md` stores discovered system knowledge.
- `plan.md` stores approved decisions and progress.

When context compacts, reload from these files and continue.

## Deliverable Checklist

Use this workflow as complete only when all are true:

- `research.md` exists and is reviewed.
- `plan.md` exists and is approved.
- Inline-note cycle completed.
- Todo checklist added and tracked.
- Implementation finished against approved scope.
- Typecheck/tests pass for changed area.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mostlyfine) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
