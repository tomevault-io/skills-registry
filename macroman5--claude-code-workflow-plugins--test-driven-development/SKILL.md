---
name: test-driven-development
description: Enforce RED→GREEN→REFACTOR micro-cycles and keep diffs minimal Use when this capability is needed.
metadata:
  author: macroman5
---

# Test-Driven Development

## Purpose
Bias implementation to tests-first and small, verifiable changes.

## Behavior
1. RED: scaffold 1–3 failing tests targeting the smallest slice.
2. GREEN: implement the minimum code to pass.
3. REFACTOR: improve names/structure with tests green.
4. Repeat in tiny increments until task acceptance criteria are met.

## Guardrails
- Block large edits unless a failing test exists.
- Prefer small diffs spanning ≤3 files.
- Keep test names explicit and deterministic.

## Output Style
- `bullet-points` for steps; `markdown-focused` for code blocks.

## Integration
- `/lazy task-exec` implementation phase; Coder/Tester agents.

## Example Prompt
> Apply TDD to implement input validation for prices.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/macroman5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
