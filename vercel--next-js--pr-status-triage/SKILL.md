---
name: pr-status-triage
description: > Use when this capability is needed.
metadata:
  author: vercel
---

# PR Status Triage

Use this skill when the user asks about PR status, CI failures, or review comments in the Next.js monorepo.

## Workflow

1. Run `node scripts/pr-status.js --wait` in the background (timeout 1 min), then read `scripts/pr-status/index.md`.
2. Analyze each `job-{id}.md` and `thread-{N}.md` file for failures and review feedback.
3. Prioritize blocking jobs first: build, lint, types, then test jobs.
4. Treat failures as real until disproven; check the "Known Flaky Tests" section before calling anything flaky.
5. Reproduce locally with the same mode and env vars as CI.
6. After addressing review comments, reply to the thread describing what was done, then resolve it. See `thread-N.md` files for ready-to-use commands.
7. When the only remaining failures are known flaky tests and no code changes are needed, retrigger the failing CI jobs with `gh run rerun <run-id> --failed`. Then wait 5 minutes and go back to step 1. Repeat this loop up to 5 times.

## Quick Commands

```bash
node scripts/pr-status.js                  # current branch PR
node scripts/pr-status.js <number>         # specific PR
node scripts/pr-status.js [PR] --wait      # background mode, waits for CI to finish
node scripts/pr-status.js --skip-flaky-check  # skip flaky test detection
```

## References

- [workflow.md](./workflow.md) — prioritization, common failure patterns, resolving review threads
- [local-repro.md](./local-repro.md) — mode/env matching and isolation guidance

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vercel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
