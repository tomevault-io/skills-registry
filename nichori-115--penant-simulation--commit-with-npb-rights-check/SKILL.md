---
name: commit-with-npb-rights-check
description: Enforce post-implementation commits and pre-commit rights checks for this project. Use when making code or content changes in penant-simulation to (1) run a real-team-name scan before commit, (2) block commits if real NPB team names appear, and (3) always finish with a git commit and commit hash report. Use when this capability is needed.
metadata:
  author: nichori-115
---

# Commit With NPB Rights Check

Execute this workflow after implementing requested changes in this repository.

## Workflow

1. Implement requested changes.
2. Run validation commands relevant to the change (at minimum `swift test` for app logic changes).
3. Stage intended files with `git add ...`.
4. Run `bash .codex/skills/commit-with-npb-rights-check/scripts/check_npb_names.sh --staged`.
5. If blocked terms are detected, replace them with fictional names and rerun step 4.
6. Commit with a clear message using `git commit -m "..."`.
7. Report the commit hash and scan result to the user.

## Rules

- Do not skip the scan before commit.
- Do not commit while scan fails.
- Treat docs, test data, and UI strings the same as source code for name checks.
- If a legitimate exception is required, ask the user before committing.

## Commands

- Staged file scan:
  - `bash .codex/skills/commit-with-npb-rights-check/scripts/check_npb_names.sh --staged`
- Full repository scan:
  - `bash .codex/skills/commit-with-npb-rights-check/scripts/check_npb_names.sh --all`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nichori-115) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
