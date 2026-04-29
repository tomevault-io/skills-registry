---
name: project-distill
description: | Use when this capability is needed.
metadata:
  author: laurigates
---

# /project:distill

Distill session insights into reusable project knowledge.

## When to Use This Skill

| Use this skill when... | Use alternative when... |
|------------------------|------------------------|
| End of session, want to capture learnings | Need to write a blog post -> `/blog:post` |
| Discovered a pattern worth reusing | Need to analyze git history for docs gaps -> `/git:log-documentation` |
| Found a CLI workflow worth saving as a recipe | Need to configure a justfile from scratch -> `/configure:justfile` |
| Want to update rules based on session experience | Need to check project infrastructure -> `/configure:status` |

## Core Principle: Update Over Add

Before proposing any artifact, evaluate: Does it update an existing one? Does an existing one already cover this? Is this genuinely new and reusable? See [REFERENCE.md](REFERENCE.md) for detailed evaluation criteria.

## Context

- Git repo detected: !`find . -maxdepth 1 -name '.git' -type d`
- Justfile: !`find . -maxdepth 1 \( -name 'justfile' -o -name 'Justfile' \) -print -quit`
- Rules directory: !`find .claude/rules -name '*.md' -type f`

## Parameters

| Parameter | Description |
|-----------|-------------|
| `--rules` | Only analyze potential rule updates |
| `--skills` | Only analyze potential skill updates |
| `--recipes` | Only analyze potential justfile recipe updates |
| `--all` | Analyze all three categories (default) |
| `--dry-run` | Show proposals without applying changes |

## Tool Call Efficiency

Minimize LLM round-trips: batch file reads in a single response, combine evaluation and redundancy checking in one pass, complete one category before starting the next.

## Execution

Execute this session distillation workflow:

### Step 1: Analyze session activity

1. Review **conversation history** — tool calls, file edits, commands run, results
2. If in a git repo, run in parallel: `git log --oneline --max-count=20` and `git log --stat --oneline --max-count=10`
3. Identify patterns, catalog tools used, note pain points

### Step 2: Evaluate and check redundancy (single pass per category)

When `--all`: complete rules -> skills -> recipes. Do not interleave.

**Rules** (`.claude/rules/*.md`): Glob all rule files, Read ALL in one response, evaluate each insight against existing rules in one pass (update/skip/remove/merge/add).

**Skills**: Glob relevant skill files (target specific plugins from Step 1), Read in one response, evaluate in one pass.

**Recipes**: Run `just --dump --dump-format json`, evaluate session commands against loaded recipes in one pass.

### Step 3: Present proposals

Categorize as: `[UPDATE]`, `[SKIP]`, `[NEW]`, or `[REDUNDANT]` with file paths and reasons.

### Step 4: Apply changes

If not `--dry-run`: use AskUserQuestion to confirm each category, then edit/create/remove.

### Step 5: Report summary

Output concise summary of changes made.

## Agentic Optimizations

| Context | Command |
|---------|---------|
| Session diff summary | `git log --stat --oneline --max-count=10` |
| Recent commits | `git log --oneline --max-count=20` |
| List justfile recipes | `just --list` |
| Dump justfile as JSON | `just --dump --dump-format json` |
| Find rules | Glob `.claude/rules/*.md` |
| Batch-read all rules | Glob then Read all results in one response |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/laurigates) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
