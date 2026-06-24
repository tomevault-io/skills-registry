---
name: code-review
description: Review the current diff or requested target for correctness bugs plus reuse, simplification, efficiency, and altitude issues. Use when the user invokes $code-review or /code-review, asks for a code review, asks to review a diff or PR, or asks for review findings with optional low, medium, high, max, --fix, or --comment controls. Use when this capability is needed.
metadata:
  author: noahkagan
---

# Code Review

Parse invocation for:

- Effort: `low`, `medium` default, `high`, `max`.
- `--fix`: apply accepted findings.
- `--comment`: post inline PR comments when possible.
- Target: PR number, branch, or file path; otherwise review the working
  diff.

Review for correctness first, then reuse/simplification/efficiency/
altitude. Prefer precise actionable findings at `low`/`medium`; prefer
recall at `high`/`max`.

## Scope

Gather the diff with `git diff @{upstream}...HEAD`, falling back to
`main...HEAD`, `HEAD~1`, and `git diff HEAD` when needed. If the user
named a target, review that instead.

## Finder angles

Correctness:

- **Line scan:** read changed hunks plus enclosing functions; look for
  wrong conditions, off-by-one, null/undefined paths, missing `await`,
  falsy-zero, copy-paste variables, swallowed errors, regex mistakes.
- **Removed behavior:** for each deleted guard/invariant, find where it
  was re-established.
- **Cross-file trace:** check changed functions' callers and callees for
  new preconditions, return shapes, exceptions, ordering, or timing.
- **Language pitfalls** (`max`): scan for language/framework footguns.
- **Wrapper/proxy correctness** (`max`): wrappers must call the wrapped
  instance and forward methods callers use.

Cleanup:

- **Reuse:** use existing helpers instead of reimplementing.
- **Simplification:** remove redundant state, copy-paste, dead code,
  deep nesting, or needless abstraction.
- **Efficiency:** avoid repeated I/O/computation, unnecessary serial
  work, and hot-path blocking.
- **Altitude:** fix at the right depth; avoid fragile special cases on
  top of shared infrastructure.

## Verification

Dedup candidates by mechanism. Verify each survivor:

- **CONFIRMED:** concrete trigger and wrong output/crash.
- **PLAUSIBLE:** realistic trigger, but runtime/config confirmation
  needed.
- **REFUTED:** contradicted by code, impossible by invariant, or already
  handled.

Keep CONFIRMED and PLAUSIBLE. In recall mode, default uncertain but
realistic runtime issues to PLAUSIBLE; do not drop them for being
environment-dependent.

## Effort recipes

### low

One diff pass, no verifier, up to 4 findings. Skip test/fixture hunks.
Report only hunk-visible runtime bugs and obvious cleanup.

Output one line per finding:
`path/to/file.ext:123 — problem and concrete failure`. If none, output
`(none)`.

### medium

Default precision mode:

- Find up to 6 candidates from line scan, removed behavior, cross-file
  trace, reuse, simplification, efficiency, and altitude.
- Verify once using CONFIRMED/PLAUSIBLE/REFUTED.
- Output JSON, up to 8 findings.

### high

Recall mode:

- Same finder set and candidate caps as medium.
- Verify once, recall-biased.
- Output JSON, up to 10 findings.

### max

Maximum recall:

- Find up to 8 candidates from all correctness and cleanup angles.
- Verify once, recall-biased.
- Sweep once for missed defects.
- Output JSON, up to 15 findings.

JSON format:

```json
[
  {
    "file": "path/to/file.ext",
    "line": 123,
    "summary": "one-sentence statement of the bug",
    "failure_scenario": "concrete inputs/state -> wrong output/crash"
  }
]
```

Rank by severity. Return `[]` when no finding survives verification.

## `--comment`

For GitHub PR targets, post each finding as an inline PR comment. Use the
available inline-comment MCP tool when present; otherwise use `gh api` or
print findings and say commenting was skipped. Include a suggestion block
only when it fully fixes the issue.

## `--fix`

Apply findings directly when the fix is local and behavior-preserving.
Skip false positives, intended behavior changes, and fixes requiring work
well outside the reviewed diff. Report fixed and skipped items.

---
> Source: [noahkagan/skills](https://github.com/noahkagan/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
