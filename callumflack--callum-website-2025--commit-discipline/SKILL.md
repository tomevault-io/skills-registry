---
name: commit-discipline
description: **NEVER commit unless the user explicitly says to commit in this conversation.** Use when this capability is needed.
metadata:
  author: callumflack
---

# Commit Discipline

## Hard gate

**NEVER commit unless the user explicitly says to commit in this conversation.**

- No `git commit`, `git commit --amend`, rebase/squash, or any git operation
  that _creates or rewrites commits_ unless explicitly asked.
- Default: make code changes, show a tight diff summary, and ask for approval.
- If the user asks for a commit message, only provide the message — do not
  commit unless they also explicitly say to commit.

## Speed rule (no back-and-forth on commit failures)

- If the user explicitly asked you to commit and the commit fails due to
  sandbox/permissions or `.git/index.lock`, automatically retry using the
  appropriate permissions and clear a stale lock (see
  `git-troubleshooting.mdc`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/callumflack) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
