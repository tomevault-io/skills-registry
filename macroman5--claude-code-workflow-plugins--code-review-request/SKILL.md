---
name: code-review-request
description: Request and process code review efficiently with a simple rubric and patch plan Use when this capability is needed.
metadata:
  author: macroman5
---

# Code Review Request

## Purpose
Summarize changes and request focused review with clear findings and an actionable fix plan.

## When to Use
- After quality pipeline passes in `/lazy task-exec`
- Before commit or before `/lazy story-review`

## Behavior
1. Summarize: files changed, purpose, risks (≤5 bullets).
2. Table rubric: Issue | Severity (Critical/Warning/Suggestion) | File:Line | Fix Plan.
3. Patch plan: 3–6 concrete steps grouped by severity.
4. Optional: produce a PR-ready comment block.

## Output Style
- `table-based` for findings; short bullets for summary and steps.

## Guardrails
- No auto-commits; propose diffs only.
- Separate criticals from suggestions.

## Integration
- Coder/Reviewer agents; `/lazy task-exec` before commit; `/lazy story-review` pre-PR.

## Example Prompt
> Request review for changes in `src/payments/processor.py` and tests.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/macroman5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
