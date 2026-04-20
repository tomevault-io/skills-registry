---
name: batch-issue-fix
description: Triage and fix multiple GitHub issues with a repeatable process. Use when this capability is needed.
metadata:
  author: plevasseur
---

# Batch Issue Fix

## Purpose

Handle a list of issue URLs by triaging them in parallel and fixing them in a consistent order.

## Inputs

- Required:
  ```
  ISSUE_URLS:
  - https://github.com/ORG/REPO/issues/123
  - https://github.com/ORG/REPO/issues/456
  ```

## Steps

1. Parse `ISSUE_URLS` from the user message in the given order.
2. For each URL, spawn a subagent to read the issue, identify likely files, and suggest tests.
3. Create a batch plan file at `$OPENCODE_CONFIG_DIR/plans/batch-issues-YYYYMMDD.md` with:
   - Ordered issue list
   - Dependencies or conflicts
   - Planned per-issue plan file names
   - Suggested test matrix
4. For each issue in order, follow the issue-fix workflow:
   - Create the per-issue plan file at `$OPENCODE_CONFIG_DIR/plans/issue-<id>-<slug>.md`
   - Implement and test
   - Summarize progress before moving on
5. If the user requested commits or PRs, follow git conventions per issue. Create PRs automatically when requested, and include `Closes #<id>` in each PR body. Otherwise, ask before committing.

## Output

- Combined triage report
- Batch plan file path
- Per-issue status updates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plevasseur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
