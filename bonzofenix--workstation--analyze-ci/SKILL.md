---
name: analyze-ci
description: Analyze failed GitHub Action jobs for the current branch's PR. Use when this capability is needed.
metadata:
  author: bonzofenix
---

# Analyze CI Failures

Analyzes logs from failed GitHub Action jobs for the current branch's PR.

## Prerequisites

- **GitHub CLI**: Authenticated via `gh auth login`
- **Current Branch**: Has an open PR on GitHub

## Usage

When invoked:
1. Detects the PR number for current branch
2. Checks status of all CI checks
3. Identifies failed jobs
4. Fetches and analyzes logs from failed jobs
5. Provides summary with root causes and error snippets

## How it Works

Uses GitHub CLI to:
- Detect current PR: `gh pr view --json number`
- List checks: `gh pr checks <pr-number>`
- View run details: `gh run view <run-id>`
- Fetch logs: `gh api repos/.../actions/jobs/<job-id>/logs`

Output includes:
- PR number and branch
- Failed job names
- Root cause analysis
- Error messages and stack traces
- Relevant log snippets
- Suggested fixes (when applicable)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bonzofenix) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
