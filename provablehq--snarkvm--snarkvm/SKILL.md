---
name: snarkvm-github
description: | Use when this capability is needed.
metadata:
  author: ProvableHQ
---

# snarkVM GitHub Context

Fetch PR or issue context from ProvableHQ/snarkVM into `.claude/workspace/`.

## Usage

```
/snarkvm-github pr <number> [--force]
/snarkvm-github issue <number> [--force]
```

## Prerequisites

```bash
gh auth status || gh auth login
```

## Fetch PR Context

```bash
SKILL_DIR="$(dirname "$(readlink -f "$0")")"
"$SKILL_DIR/scripts/fetch-pr.sh" $ARGUMENTS
```

Produces: `context-pr-N.json`, `files-pr-N.txt`, `commits-pr-N.json`, `comments-pr-N.json`, `checks-pr-N.json`, `threads-pr-N.jsonl`, `unresolved-pr-N.json`, `resolved-pr-N.json`, `linked-issues-pr-N.txt`, `state-pr-N.md`

## Fetch Issue Context

```bash
SKILL_DIR="$(dirname "$(readlink -f "$0")")"
"$SKILL_DIR/scripts/fetch-issue.sh" $ARGUMENTS
```

Produces: `context-issue-N.json`, `comments-issue-N.jsonl`, `timeline-issue-N.json`, `linked-prs-issue-N.txt`, `state-issue-N.md`

## Quick Refresh (PR threads only)

```bash
SKILL_DIR="$(dirname "$(readlink -f "$0")")"
"$SKILL_DIR/scripts/refresh-threads.sh" <pr_number>
```

## Caching

Context is cached for 1 hour. Use `--force` to bypass.

## Integration

This skill provides context for **snarkvm-review**, **snarkvm-fix pr**, and **snarkvm-fix**. Those skills auto-fetch if context is missing.

---
> Source: [ProvableHQ/snarkVM](https://github.com/ProvableHQ/snarkVM) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-29 -->
