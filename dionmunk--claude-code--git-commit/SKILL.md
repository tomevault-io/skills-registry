---
name: git-commit
description: Suggest Conventional Commit message(s) from current changes and provide a copy-ready snippet. Use when this capability is needed.
metadata:
  author: dionmunk
---

You are running in a VS Code workspace.

Goal:
Analyze the current git changes and suggest high-quality Conventional Commit message(s).
Prefer staged changes when present. Do NOT create commits—only suggest messages.

STRICT SAFETY RULE:
- You MUST NOT run `git commit`, `git push`, or modify git history in any way.
- You MUST ONLY analyze repository state and OUTPUT suggested commit message(s).

Hard Requirements:
- All suggestions MUST follow Conventional Commits:
  <type>(optional-scope): <subject>
- Allowed types: feat, fix, docs, style, refactor, perf, test, build, ci, chore, revert
- Subject rules:
  - imperative mood
  - no trailing period
  - <= 72 characters
- Scope: optional but recommended when obvious
- If a breaking change is detected, use '!' and include a BREAKING CHANGE note.
- Do not invent changes that are not present in git status/diff/log.
- NEVER add co-author information or Claude attribution
- Commits should be authored solely by the user
- Do not include any "Generated with Claude" messages
- Do not add "Co-Authored-By" lines
- Write commit messages as if the user wrote them

Process:
1) Collect git context using the Bash tool with these read-only commands in parallel:
   - `git rev-parse --show-toplevel`
   - `git status --porcelain=v1`
   - `git diff --stat`
   - `git diff`
   - `git diff --staged --stat`
   - `git diff --staged`
   - `git log -n 10 --oneline --decorate`

2) Determine review target:
   - If staged changes exist, analyze the staged diff output
   - Otherwise, analyze the unstaged diff output

3) Analyze changes:
   - Identify primary intent (feat, fix, refactor, docs, test, chore, build, ci).
   - Infer scope from paths/modules when clear.
   - Detect multiple concerns and recommend splitting if appropriate.
   - Detect breaking changes:
     - removed/renamed public APIs
     - behavior changes
     - schema changes
     - incompatible config changes

Output Format (exactly):

A) Summary
- 2–4 bullets describing what changed
- Whether staged or unstaged diffs were used
- Whether multiple commits are recommended

B) Recommended Commit(s)
- If a single commit is appropriate, show one.
- If multiple commits are cleaner, show them in order.

C) Copy-Ready Commit Message(s)

Rules:
- Use triple backtick code blocks for both `git add` commands and commit messages
- One commit message per code block
- No commentary inside code blocks
- Ready to paste into the terminal
- If a description body is included, format each item as a bullet point using `-`
- Group files by directory when possible (e.g. `git add backend/` instead of listing every file)

Single commit format:
- Show the `git add` command in a code block, then the commit message in a separate code block.
- If all changes are already staged, omit the `git add` block.
- Example:

  ```
  git add src/auth/ src/models/user.ts
  ```
  ```
  feat(auth): add OAuth2 login flow

  - Add Google and GitHub provider support
  - Implement token refresh logic
  - Update user model with provider fields
  ```

Multiple commit format:
- Number each commit with a "Commit N:" header
- Under each header, show the `git add` command in a code block, then the commit message in a separate code block
- Use `git reset HEAD` before the first group if files are already staged incorrectly.
- Example:

  Commit 1:
  ```
  git add src/auth/ src/models/user.ts
  ```
  ```
  feat(auth): add OAuth2 login flow

  - Add Google and GitHub provider support
  - Implement token refresh logic
  - Update user model with provider fields
  ```
  Commit 2:
  ```
  git add src/auth/__tests__/
  ```
  ```
  test(auth): add OAuth2 login tests
  ```

Quality Bar:
- Prefer refactor over feat if behavior did not change.
- Prefer chore(deps) for dependency updates.
- Prefer build/ci for pipeline/config changes.
- Prefer docs for documentation-only changes.
- Prefer test when only tests change.
- Keep suggestions realistic and grounded in the diff.

Now run the process and produce the output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dionmunk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
