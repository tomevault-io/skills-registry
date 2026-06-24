---
name: code-review
description: Use as the single front door for reviewing a PR, branch, commit, or diff. Default to until-clean review: map changed surfaces, audit rubbish/churn, run relevant correctness/security/UI/architecture/cognitive-load passes, use Codex review plus cold review, fix actionable findings, validate, and report the final verdict. Trigger for review this PR, audit this diff, is this mergeable, find bugs, Codex review this, run /review, prepare this PR, fix findings, keep reviewing until clean, or explain/understand this PR. Use when this capability is needed.
metadata:
  author: jesse-merhi
---

# Code Review

Use this as the parent review skill. Load focused sub-skills yourself as needed
and mention only the passes that actually ran.

## Modes

- `until-clean`: default. Review, fix actionable findings, validate, and
  re-review until clean or blocked.
- `understand`: only when the user asks to understand, orient, or choose a
  reading order. Explain the diff; do not judge merge readiness unless asked.
- `map-only`: only when the user explicitly asks for a review map.
- `security-only`: only when the user explicitly asks for a supply-chain or
  security-only review.
- `read-only`: only when the user explicitly says not to edit. Map and review,
  then stop before fixes.

Keep organization-specific review bots, merge workflows, security remediation,
and advisory writing separate unless explicitly requested.

## Pass Order

1. Resolve the target.
   Prefer a PR number/URL. Otherwise use the current branch diff, explicit
   range, or commit. Record the Codex review target too:
   - dirty checkout -> `codex review --uncommitted`
   - branch/PR -> `codex review --base origin/<base>`
   - immutable commit -> `codex review --commit <sha>`

2. Load project-specific context when available.
   Use repo-local docs, glossary, architecture notes, stale-doc warnings, and
   safety invariants. If no durable context exists, continue with the code,
   tests, and local history.

3. Load `review-surface-map`.
   Map changed flows, entrypoints, contracts, side effects, state transitions,
   risk surfaces, and validation targets before judging correctness.

4. Add optional passes only when relevant.
   - `reviewing-pr-by-feature`: large, AI-generated, or unclear diffs.
   - `pr-rubbish-audit`: broad, noisy, generated-heavy, deletion-heavy, or
     suspiciously unrelated diffs.
   - `supply-chain-security-pass`: CI, workflows, dependencies, lockfiles,
     scripts, permissions, secrets, generated/vendor files, or code execution.
   - `frontend-ui-validation`: rendered UI changes where screenshots or
     computed styles materially affect confidence.
   - `improve-codebase-architecture`: boundary, ownership, dependency, or
     refactor-shape concerns.
   - `reducing-cognitive-load`: dense, clever, stringly typed, weakly typed,
     over-abstracted, or hard-to-maintain code.
   - `monitoring-gh-actions`: PRs with pending GitHub Actions checks.

5. Review for correctness.
   Read callers, callees, tests, docs, config, and prior history as needed.
   Run focused checks when they can raise confidence.

6. Run Codex review closeout.
   Prefer `scripts/codex-review` from this skill. If it is unavailable, use
   bare `codex review` with only the target flag. Do not pass inline prompts,
   desired verdicts, prior rationale, or JSON-format instructions.

7. Run independent cold review.
   Use `cold-pr-review` or `cold-pr-review-until-clean` for thoroughness,
   merge readiness, bug finding, substantial PRs, or when the implementer was
   close to the work. Give the reviewer only the target and a neutral checklist.

8. Load `finding-discipline`.
   Keep only concrete actionable findings. Drop style nits, vague risks,
   generic missing-test comments, duplicates, and weak observations.

9. Check context maintenance.
   If the diff changes durable project context, update external context only in
   `until-clean` and only when evidence-backed. In read-only runs, recommend
   the context update instead.

## Codex Review Contract

Follow these closeout rules:

- Treat Codex review output as advisory. Verify every accepted finding by
  reading the real code path and adjacent files.
- Read dependency docs/source/types when a finding depends on external
  behavior.
- Reject unrealistic edge cases, speculative risks, broad rewrites, and fixes
  that over-complicate the codebase.
- Prefer small fixes at the right ownership boundary.
- Keep going until Codex review returns no accepted/actionable findings, unless
  a safety cap, tool failure, validation blocker, human decision, or user stop
  interrupts the loop.
- Never switch or override the review model. If the review hits model capacity,
  retry the same command a few times with the same model.
- If rejecting a finding as intentional or not worth fixing, add an inline code
  comment only when it records a real invariant or ownership decision future
  reviewers need.
- Do not push just to review. Push only when the user requested publish, ship,
  or PR update.
- Format before review when formatting would move line numbers materially.
- Tests and Codex review may run in parallel after the diff is stable. If either
  side causes edits, rerun affected validation and Codex review.
- Do not run another Codex review just for prettier closeout wording, a second
  opinion, or a clearer clean line.

## Helper

Use `scripts/codex-review` from this skill for Codex closeout when possible.
It selects dirty/branch/commit targets, fetches branch bases, supports
`--parallel-tests`, and prints `codex-review clean: no accepted/actionable
findings reported` when clean.

Recommended forms:

```sh
scripts/codex-review
scripts/codex-review --mode branch
scripts/codex-review --mode commit --commit HEAD
scripts/codex-review --parallel-tests "<focused test command>"
```

For PR/branch work, leave the helper in `--mode auto` or force
`--mode branch`. Do not force local mode after committing; a clean
`--uncommitted` review only proves there is no local patch.

## Until-Clean Loop

Maintain:

```text
iteration = 0
max_iterations = 8 unless the user requests otherwise or a local override removes it
last_reviewed_head = <current HEAD or PR head SHA>
```

Loop:

1. Run the pass order above.
2. Fix actionable `P0`, `P1`, and `P2` findings. Fix `P3` only when cheap,
   low-risk, or requested.
3. Run relevant validation for touched surfaces.
4. Re-review with Codex closeout and cold review until clean.
5. Stop honestly on cap hit, unavailable tools, validation blockers, or
   findings needing human/product/security judgment.

Check `~/.codex/AGENTS.override.md` before enforcing the cap; local overrides
may raise or remove it.

## Clean Stop Rule

Stop as clean only when:

- no actionable `P0`, `P1`, or `P2` findings remain
- relevant validation commands pass
- supply-chain and UI validation are clean when applicable
- the final diff has no unrelated rubbish or accidental generated drift
- cold review is clean for merge-readiness or until-clean confidence
- skipped checks and residual risks are explicit and non-blocking

For auth, permissions, secrets, migrations, release/publish, dependency
resolution, CI execution, or broad data flow, require two clean review passes on
the same final diff when practical.

## Output

For `understand`, lead with `Review Map`, `Walkthrough`, `Risk Surfaces`, and
`Validation Targets`.

For `until-clean`, report iterations, phases run, findings fixed, validation
commands and results, context updates, final verdict, and anything left for
human judgment. When there are no findings, say so plainly and name real test
gaps or residual risk.

---
> Source: [jesse-merhi/skills](https://github.com/jesse-merhi/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
