---
name: pr-review
description: Complete PR code review workflow Use when this capability is needed.
metadata:
  author: ccsert
---

# PR Code Review Skill

This skill orchestrates a complete code review for a Gitea/Forgejo pull request.

## Steps

1. Fetch the PR diff using `gitea-pr-diff`
2. Analyze the code changes
3. Identify issues, improvements, and good practices
4. Submit a structured review using `gitea-review`

## Usage

When asked to review a PR, I will:
- Get the full diff with line numbers
- Review each file for bugs, security issues, and improvements
- Create line-level comments for specific issues
- Write a summary of the overall changes
- Submit the review with appropriate approval status

## Trigger Phrases

- "review PR #123"
- "review this pull request"
- "@opencode review"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ccsert) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
