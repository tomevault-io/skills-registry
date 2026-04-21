---
name: testing
description: Write test cases to build confidence in code correctness and identify edge cases or regressions. Use when this capability is needed.
metadata:
  author: gargraham
---
# Skill: Testing

> See also: `AGENT_SYSTEM.md`

## Phase
- Validate (proactive) or Change (when fixing broken tests)

## Purpose
- Build confidence in correctness by writing test cases that verify behavior.
- Identify edge cases and regressions early.

## Use When
- "Write tests for this"
- "Test coverage is low"
- "Add a test to verify X"
- "Fix failing tests"

## Inputs to Request (only if blocking)
- What code or behavior should be tested?
- What test framework/runner is available?
- Coverage target (if any)?

## Workflow
- Understand the code:
  - What's the happy path?
  - What are edge cases (null, empty, errors)?
  - What should fail gracefully?
- Plan test cases (small scope):
  - 3–5 core scenarios per function/component
  - At least one failure case
- Implement tests:
  - Use existing test framework
  - Follow repo style
  - Keep tests fast and isolated
- Proof of Life:
  - All tests pass
  - Run command: `npm test` (or equivalent)
  - Show test output

## Output
- **Test cases added** (brief list)
- **Coverage impact** (if measurable)
- **Proof of Life** (test run output)

## Memory
- Append one line to JOURNAL.md if this closes a testing gap

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gargraham) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
