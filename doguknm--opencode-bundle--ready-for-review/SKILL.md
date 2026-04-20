---
name: ready-for-review
description: Prepare concise review package with summary, risk, tests, files, and follow-ups Use when this capability is needed.
metadata:
  author: doguknm
---

# Ready for Review

Use to package a change before requesting/receiving review.

## Steps
1) Summarize
- What changed and why (1-3 bullets)

2) Risk and impact
- Note user-facing or operational risks; call out migrations/rollouts

3) Tests
- List commands run and results; note unrun/blocked tests

4) Files touched
- Highlight key files and sensitive areas

5) Follow-ups
- TODOs, flags to remove, monitoring to check post-merge

6) Next action
- If ready, trigger requesting-code-review skill; otherwise keep local

## Outputs
- Short review bundle: summary, risks, tests, files, follow-ups

## When to stop and ask
- If critical tests cannot run or results are unclear
- If risk is high and needs a deeper plan

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/doguknm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
