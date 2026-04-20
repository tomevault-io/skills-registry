---
name: thesis-disagreement-report
description: Generate a report of keyword-vs-LLM disagreements to guide improvements Use when this capability is needed.
metadata:
  author: nikhillinit
---

# thesis-disagreement-report

## When to use
- You want to find where keyword gating is too permissive or too strict compared to LLM.

## Inputs
- days window.
- keyword threshold for treating keyword_score as 'match'.
- optional output path.

## Workflow
1. Run: `python -m ops.cli quality thesis-disagreement-report --days 30 --keyword-threshold 0.4 --out /tmp/report.md`.
2. Review examples and adjust keyword rules or negative keywords accordingly.

## Outputs
- Markdown report listing example signal_ids for keyword FP/FN.

## Guardrails
- Disagreement does not imply keyword is wrong; validate against real outcomes whenever possible.

## References
- `references/reference.md`
- `docs/QUALITY_OPS_ARCHITECTURE.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nikhillinit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
