---
name: looper
description: Persistence skill for iterative development. Prevents premature exit and encourages continued effort until completion criteria are met. Use when this capability is needed.
metadata:
  author: spenceriam
---

# Looper Skill

This skill implements a persistence pattern for iterative, self-referential development loops.

## What it is

A methodology for continuous iteration on a task until completion criteria are met or clear blockers are documented.

## What it does

- Prevents the agent from giving up prematurely when tasks are difficult.
- Encourages multiple attempts with adjusted approaches.
- Treats failures as information for the next iteration.
- Makes progress visible through explicit documentation of attempts and blockers.

## Why it is important

AI agents often exit early when they encounter ambiguity, missing context, or partial failure. The looper skill reframes setbacks as learning opportunities and encourages continued effort. Success often depends on persistence and prompt refinement, not just on the first attempt.

## Process

When facing a difficult task:

1. **Attempt**
   - Make a concrete attempt at the task based on current understanding.

2. **If blocked, document**
   - Describe what is blocking progress (missing information, unclear requirements, technical constraints).
   - Record what you tried and what happened.

3. **Analyze and propose alternatives**
   - List at least one alternative approach that might overcome the blocker.
   - Consider simplifying assumptions, smaller subproblems, or different tools.

4. **Try the next approach**
   - Select an alternative approach and attempt it.

5. **Repeat**
   - Continue iterating until:
     - Completion criteria are met, or
     - Maximum iterations are reached and blockers are clearly documented.

## Completion signals

- The task meets all specified success criteria.
- All relevant tests pass.
- All requirements are implemented for the current scope.
- Or: maximum iterations reached with well-documented blockers and recommendations.

The looper skill is compatible with any harness and should be applied whenever a task proves more challenging than expected.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spenceriam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
