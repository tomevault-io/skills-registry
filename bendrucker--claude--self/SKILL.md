---
name: reviewself
description: | Use when this capability is needed.
metadata:
  author: bendrucker
---

# Self Review

Review your own code changes before committing or requesting peer review.

## Usage

### Direct mode

```bash
bunx difit $ARGUMENTS
```

Common arguments:
- `staged` — Staged changes only
- `working` — Unstaged changes only
- `@ main` — Compare HEAD with main branch

### Stdin mode

Pipe a git diff for full control over the diff content:

```bash
git diff $ARGUMENTS | bunx difit
```

Common arguments:
- `HEAD` — All uncommitted changes (staged + unstaged)
- `--merge-base main` — Changes since diverging from main

Use stdin mode for all uncommitted changes (`git diff HEAD`) or when using git diff flags (e.g., `--merge-base`, revision ranges).

## Workflow

1. Run `difit` with the target diff
2. User reviews in the browser and adds comments on specific lines
3. When the user closes the browser tab, comments are output to stdout
4. Parse the comments and apply the requested changes

## Comment Format

Comments are output in this format:

```
📝 Comments from review session:
==================================================
path/to/file.ts:L42
Comment text here
=====
path/to/other.ts:L10-L20
Comment on a range of lines
==================================================
Total comments: 2
```

## Applying Feedback

For each comment:
1. Read the referenced file and lines
2. Understand the requested change
3. Apply the edit using the Edit tool
4. Confirm the change was made correctly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bendrucker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
