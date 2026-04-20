---
name: documentation-maintenance
description: Maintain and improve technical documentation with a repeatable audit loop (measure, fix, verify, report) across any repository. Use when this capability is needed.
metadata:
  author: segnities007
---

# Documentation Maintenance Skill

## When to use

Use this skill when the user asks to review, update, or continuously improve docs such as design docs, ADRs, standards, runbooks, or guides.

## Goal

Produce documentation that is correct, consistent, traceable, and easy to review.

## Workflow

1. Set scope
- Identify target docs and related source files.
- Define what "done" means for this pass.

2. Measure current quality
- Check metadata consistency (`Last Updated`, owner, status, links).
- Scan for stale terms, broken references, and duplicated rules.

3. Fix with minimal diffs
- Update source-of-truth documents first.
- Sync derivative docs (index pages, summaries, onboarding docs).
- Keep wording concise and testable.
- Continue the fix loop until all discovered in-scope issues are resolved, unless blocked.

4. Verify
- Re-check stale terms and links.
- Confirm examples and commands still match current implementation.

5. Report
- Summarize file-level changes.
- List remaining risks and every unresolved task (with blocker/assumption).

## Quality rubric (0-5 each)

- Accuracy
- Consistency
- Completeness
- Traceability
- Maintainability
- Verifiability
- Readability
- Discoverability
- Non-duplication
- Iterability

## Output format

- Target level (Correctness / Operability / Self-improving)
- Change policy (max 3 lines)
- Edited files
- Before/After quality notes
- Evidence sources (official links or repo source)
- Resolved tasks (all)
- Unresolved tasks (all, with blockers)

## Guardrails

- Prefer source-of-truth first, then downstream docs.
- Never hide uncertainty; state assumptions explicitly.
- Avoid broad rewrites unless the user asks for restructuring.
- Default behavior is full in-scope repair, not "top N" sampling.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/segnities007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
