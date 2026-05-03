---
name: gh-pr-review-threads
description: Automate handling GitHub PR review threads using the gh-pr-review extension. Use when asked to fetch review threads, reply to inline comments, resolve or unresolve threads, or summarize/close PR review feedback via gh pr-review commands. Use when this capability is needed.
metadata:
  author: gevikhn
---

# gh-pr-review threads workflow

## Scope
- Use the gh-pr-review extension (not core gh) for review threads and replies.
- Apply when a user wants to read review threads, respond, and resolve them.

## Preconditions
- Ensure gh-pr-review is installed and available as `gh pr-review`, if not installed, use `gh extension install agynio/gh-pr-review` install extension.
- Work from the repo root or pass `--repo owner/repo` and `--pr <number>`.

## Procedure
1) Confirm commands
- Run `gh pr-review --help` if unsure about subcommands or flags.
- Use the dashed command: `gh pr-review`, not `gh pr-review` without the dash.

2) Fetch review threads
- Run:
  - `gh pr-review review view --repo <owner/repo> <pr-number>` to fetch review + comments.
  - `gh pr-review review view --repo <owner/repo> <pr-number> --unresolved` to fetch unresolved review + comments.
  - `gh pr-review threads list --repo <owner/repo> --pr <pr-number>` to list thread IDs.
- Record `threadId`, `path`, and `line` for each thread.

3) Reply to each thread
- Compose a concise, direct reply describing the fix or rationale.
- Use:
  - `gh pr-review comments reply --repo <owner/repo> --pr <pr-number> --thread-id <threadId> --body "<reply>"`

4) Resolve threads after reply
- Use:
  - `gh pr-review threads resolve --repo <owner/repo> --pr <pr-number> --thread-id <threadId>`

5) Verify all threads resolved
- Re-run:
  - `gh pr-review threads list --repo <owner/repo> --pr <pr-number>`
- Confirm all threads show `isResolved: true`.

## Error handling
- If `command not found`: instruct to install gh-pr-review.
- If `accepts at most 1 arg(s)`: check command format; ensure `--repo` and `--pr` are flags.
- If permission errors occur: report inability to reply/resolve and ask the user to handle manually.

## Output expectations
- Summarize actions taken: threads replied to, resolved, and any remaining open threads.
- Provide exact commands used if the user needs auditability.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gevikhn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
