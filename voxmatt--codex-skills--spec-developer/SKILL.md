---
name: spec-developer
description: Create and iteratively refine a complete project SPEC in SPEC.md through structured user interviews. Use when starting a new project spec, when an existing SPEC.md is partial, or when Codex must gather deep technical/product/UX decisions and tradeoffs before implementation. Use when this capability is needed.
metadata:
  author: voxmatt
---

# Spec Developer

## Overview

Create a high-quality `SPEC.md` by starting from a minimal shell, then driving repeated in-depth interviews via `$ask-user-question` until the spec is complete and implementation-ready.

## Workflow

1. Bootstrap `SPEC.md`
2. Analyze gaps and risk
3. Run iterative interview rounds
4. Update `SPEC.md` after every round
5. Finalize when completeness gate passes

## Step 1: Bootstrap SPEC.md

If `SPEC.md` does not exist:

1. Prompt for baseline inputs:
- Problem statement
- Target users
- Primary goals
- Non-goals
- Constraints (time, budget, tech, compliance)
- Expected delivery horizon
2. Create `SPEC.md` using `references/spec-template.md`.
3. Mark unknown items as `TBD` with explicit questions.

If `SPEC.md` exists:

- Read it fully and continue from Step 2.

## Step 2: Gap and Risk Scan

Compare `SPEC.md` against `references/spec-completeness-checklist.md`.

- Mark each section as: `complete`, `weak`, or `missing`.
- Prioritize interview targets by risk:
1. Decisions that affect architecture/data contracts
2. Decisions that affect safety/compliance/security
3. Decisions that affect user experience and rollout risk

## Step 3: Interview in Rounds (Use $ask-user-question)

For each round:

1. Invoke `$ask-user-question` with current spec context and unresolved high-risk topics.
2. Require non-obvious questions (failure modes, tradeoffs, operational realities, edge cases).
3. Ask only 3-5 questions per round.
4. Capture answers as concrete decisions.

Keep interviewing continuously until completion criteria pass. Do not stop after one round.

## Step 4: Update SPEC.md Continuously

After each interview round:

1. Write new decisions directly into `SPEC.md`.
2. Move resolved `TBD` items to final statements.
3. Keep a short "Open Questions" section for unresolved items.
4. Add a "Decision Log" entry with rationale for major tradeoffs.

## Step 5: Completeness Gate

Finalize only when all are true:

- All checklist sections are `complete` or explicitly deferred.
- No unresolved high-risk questions remain.
- Technical approach, UX behavior, and rollout plan are implementable without guesswork.
- Success metrics and acceptance criteria are testable.

If any gate fails, continue interviews.

## Output Rules

- Keep `SPEC.md` concrete and implementation-oriented.
- Prefer explicit thresholds, limits, and behavioral rules over generic language.
- Record rejected alternatives for major decisions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/voxmatt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
