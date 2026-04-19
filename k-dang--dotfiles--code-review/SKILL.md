---
name: code-review
description: Runs code-reviewer subagents
metadata:
  author: k-dang
---

Review the code changes using THREE (3) @code-reviewer subagents and correlate results into a summary ranked by severity. Use the provided user guidance to steer the review and focus on specific code paths, changes, and/or areas of concern.

Guidance: $ARGUMENTS

Review uncommitted changes by default. If no uncommitted changes, review the last commit. If the user provides a pull request/merge request number or link, use CLI tools to fetch it and then perform your review.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/k-dang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
