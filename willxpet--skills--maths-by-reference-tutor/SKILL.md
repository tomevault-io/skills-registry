---
name: math-by-reference-tutor
description: Generates a complete maths learning module anchored in physics and/or finance: lecture, tiered exercises, misconception-driven trick questions, Python implementation scaffold with pytest tests, and a compact solution manual. Use when user says "teach me [math topic]", "make a lecture + exercises", "give me practice problems", "ramp up difficulty", "give trick questions", "add coding scaffold + tests", or "make a solution manual", especially when they want physics/finance intuition alongside formal maths.
metadata:
  author: willxpet
---

# Math-by-Reference Tutor (Physics + Finance)

## Inputs (defaults)
- topic: required
- level: intro | intermediate | advanced (default intermediate)
- track: physics | finance | both (default both)
- num_exercises_per_tier: default 6
- num_trick_questions: default 6
- deps: stdlib + numpy only (unless user explicitly allows more)

If topic is ambiguous, choose the most standard interpretation and note it in README.md as "Assumption: …".

## Module bundle

Output these files using `=== /module/<path> ===` headers. No code fences around markdown; use code fences for Python files.

```
/module/README.md
/module/lecture.md
/module/exercises.md
/module/trick_questions.md
/module/solution_manual.md
/module/rubric.md
/module/implementation/scaffold.py
/module/implementation/tests/test_scaffold.py
```

## Anchoring (R1)

Every core idea must be anchored in the selected track(s) using concrete applications, not analogies.

Physics anchors — use at least one of: dimensional analysis, limiting cases, conservation/invariants, geometric meaning, differential equation interpretation.

Finance anchors — use at least one of: no-arbitrage/replication, convexity/monotonicity, conditioning/filtration, tails/stress, optimisation constraints.

## Lecture (R2)

Structure: motivation → core definitions → two key properties/theorems (with proof sketches appropriate to level) → worked example(s) per track → failure modes + invariant checks.

Keep concise (~900 words max).

## Exercises (R3)

Three tiers (A/B/C) with monotonically increasing difficulty. Each exercise states expected answer format.

Exercise types to mix across tiers: compute, prove/justify, translate (physics↔finance), diagnose (spot errors), approximate (limits/asymptotics), numerics (implement + compare), interpret (domain meaning).

## Trick questions (R4)

Each includes the question, "Most people do wrong:" (1 sentence), and "Minimal correction:" (1 sentence). No duplicates; each maps to a distinct failure mode.

## Implementation + tests (R5)

scaffold.py: core functions/classes with TODO markers + runnable `__main__` demo.

test_scaffold.py: tests that fail before TODOs are completed. Include identity/invariant tests, numerical sanity checks, limit-case tests, and one random property-style test (stdlib random + numpy only, no external property libs). Use pytest.

## Solution manual (R6)

Compact (~1200 words max). Include compressed derivations, reusable templates ("if you see X, do Y"), a verification checklist, and a "when to use what" decision guide.

## Rubric (R7)

Mastery gates per tier: A = mechanical competence, B = conceptual transfer, C = synthesis/novel problems.

## Workflow
1. Parse inputs and set defaults.
2. Draft prerequisites and scope boundary.
3. Build minimal concept order (internal).
4. Write lecture.md.
5. Write exercises.md.
6. Write trick_questions.md.
7. Write scaffold.py with TODOs + demo.
8. Write test_scaffold.py with fail-first behaviour.
9. Write solution_manual.md + rubric.md.
10. Emit bundle with path headers.

## Complexity bump (when user asks "harder")

Adjust along 1–2 axes only and state them in README.md:
- algebraic complexity
- abstraction/proof depth
- numerical stability
- modelling realism (physics: non-idealities; finance: tails/constraints)
- generalisation (dimension, nonlinearity)

Regenerate only affected files.

## Common issues

**Topic too broad**: pick a standard slice, state it in assumptions, list "next slices."

**Prerequisites too advanced for level**: add a prereq mini-bridge (≤200 words) at the start of lecture.md.

**Tests too confusing**: ensure tests fail only at TODO-related points with clear messages indicating what to implement next.

## Validation script

Validate a generated module bundle: `python scripts/validate_bundle.py`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willxpet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
