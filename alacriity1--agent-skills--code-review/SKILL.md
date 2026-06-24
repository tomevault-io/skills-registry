---
name: code-review
description: Review code changes, diffs, commits, branches, or PRs for correctness, regressions, security-sensitive mistakes, maintainability issues, and missing validation before commit, PR, merge, or closeout. Use when this capability is needed.
metadata:
  author: Alacriity1
---

# Code Review

Use this skill when the user asks for a code review, second pass, pre-commit check, PR review, bug-risk review, or closeout review after non-trivial code edits.

This skill is for reviewing code. It may recommend fixes or apply small fixes when requested, but it should not become a broad refactor, redesign, or style-only cleanup pass.

## Contract

- Treat review findings as hypotheses until verified against the real code path.
- Read the changed files, adjacent callers/callees, and relevant tests before judging a finding.
- Check dependency docs, source, or types when behavior depends on an external API or library.
- Prioritize correctness, regressions, security-sensitive behavior, data loss, race conditions, error handling, and test coverage.
- Avoid speculative edge cases, broad rewrites, taste-based style comments, or fixes that over-complicate the codebase.
- Prefer no finding over a weak or speculative finding.
- Prefer small, local fixes at the right ownership boundary.
- Preserve existing project conventions unless they are part of the problem.
- Do not push, commit, or open a PR unless the user explicitly asks.
- Lead final responses with findings, ordered by severity, using concrete file and line references when possible.

## Pick Review Target

Choose the smallest target that matches the user's request.

### Dirty local changes

Use when files are staged, unstaged, or untracked:

```bash
git status --short
git diff --stat
git diff
```

Include staged changes when relevant:

```bash
git diff --cached
```

### Branch or PR changes

Use when reviewing a branch against its base:

```bash
git diff --stat origin/main...HEAD
git diff origin/main...HEAD
```

If a PR exists, use its actual base when available:

```bash
base=$(gh pr view --json baseRefName --jq .baseRefName)
git diff --stat "origin/$base...HEAD"
git diff "origin/$base...HEAD"
```

Fetch remote refs only when needed for an accurate branch/PR review. If network access is unavailable or fetching is inappropriate, use local refs and state that limitation.

### Single commit

Use when reviewing one committed change:

```bash
git show --stat --oneline HEAD
git show HEAD
```

## Workflow

1. Identify the review target, user constraints, and whether fixes are requested.
2. Inspect the diff first, then inspect surrounding code needed to understand each changed path.
3. Map changed behavior to callers, state, errors, permissions, data flow, and tests.
4. Verify each potential issue before reporting it.
5. Check whether existing or missing tests would catch the changed behavior.
6. Reject findings that are intentional, already covered, not reachable, or not worth the added complexity.
7. If making fixes, keep them minimal and rerun the most focused validation.
8. Stop when there are no accepted/actionable findings for the requested target.

## Review Checklist

Focus on the areas that apply to the change:

- Correctness: logic errors, broken invariants, off-by-one issues, bad assumptions, incomplete state updates.
- Regressions: changed public behavior, compatibility breaks, migration risks, missing fallbacks.
- Error handling: swallowed errors, misleading messages, unsafe retries, missing cleanup.
- Security-sensitive behavior: auth/permission checks, input validation, injection paths, secret handling, unsafe file/network access.
- Concurrency and state: races, stale caches, ordering issues, duplicate side effects, reentrancy where relevant.
- Data integrity: serialization, parsing, rounding, units, schema changes, persistence, destructive operations.
- Tests: missing coverage for changed behavior, weak assertions, untested failure paths, brittle snapshots.
- Maintainability: confusing ownership boundaries, unnecessary coupling, duplicated complex logic.

## Validation

Run the narrowest useful checks for the reviewed area. Prefer existing project commands from `package.json`, `Makefile`, CI config, README, or `AGENTS.md`.

Common examples:

```bash
npm test
npm run lint
npm run typecheck
pnpm test
pytest
cargo test
go test ./...
forge test
```

If validation is expensive, unavailable, or unrelated to the change, state what was skipped and why.

## External Review Tools

If the environment provides a dedicated review command, use it as an advisory pass only after understanding the diff yourself.

Examples:

```bash
codex review --uncommitted
codex review --base origin/main
codex review --commit HEAD
```

Do not blindly apply tool findings. Verify each one in the code, accept only actionable findings, and rerun focused validation after any review-triggered fix.

## Output

Final response should start with findings. Use this shape:

- Accepted findings ordered by severity. Include `path:line`, impact, and the smallest useful fix direction.
- If there are no actionable findings, say that directly.
- Review target inspected.
- Fixes made, if any.
- Validation run, or why validation was skipped.
- Remaining risks or assumptions.

Do not lead with a summary before findings. Mention rejected or intentionally ignored findings only when useful to explain judgment.

---
> Source: [Alacriity1/agent-skills](https://github.com/Alacriity1/agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
