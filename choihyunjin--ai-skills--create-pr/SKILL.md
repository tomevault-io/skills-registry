---
name: create-pr
description: Create structured commits and a draft GitHub PR from current git changes. Use when the user asks to prepare commits/PR, run `/create-pr`, or wants commit-plan, commit messages, and PR body generated with optional JIRA. Use when this capability is needed.
metadata:
  author: choihyunjin
---

# Create PR

Use this skill to turn local changes into clean commits and a draft PR.

## Workflow

1. Check change status with `git status -sb`.
2. Group changed files by feature/topic and propose a commit plan:
   - file list per commit
   - recommended `type` and `scope`
   - commit message draft
3. Confirm or collect minimum metadata from user:
   - JIRA ticket (optional)
   - scope
4. Build commit messages with:
   - with JIRA: `{type}({scope}): [{JIRA}] 작업 요약`
   - without JIRA: `{type}({scope}): 작업 요약`
5. Unless user says otherwise, run:
   - `git add`
   - `git commit`
   - `git push`
6. Create draft PR with assignee `@me`:
   - auto-detect `BASE_BRANCH` from nearest merge-base branch
   - run `gh pr create --base "$BASE_BRANCH" --head "$HEAD_BRANCH" --draft --assignee @me`

## Commit Conventions

- Allowed `type`: `build`, `chore`, `ci`, `docs`, `feat`, `fix`, `perf`, `refactor`, `revert`, `style`, `test`
- `scope`: project/package area (example: `a-peach`, `admin`)
- Prefer one-line, behavior-focused summaries

## PR Content Rules

- Title:
  - with JIRA: `{type}({scope}): [{JIRA}] 작업 요약`
  - without JIRA: `{type}({scope}): 작업 요약`
- Body:
  - `배경`: why this change is needed
  - `작업 내용`: feature-level summary (do not paste raw commit logs)
  - `리뷰 마감`: within 48 working hours from PR creation

## If PR Already Exists

1. Re-check local changes.
2. Add commits with the same rules.
3. Push updates so the existing PR refreshes automatically.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/choihyunjin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
