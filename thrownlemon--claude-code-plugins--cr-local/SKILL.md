---
name: cr-local
description: Local code review using CodeRabbit CLI. Use when user wants to review changes before committing or pushing. Use when this capability is needed.
metadata:
  author: thrownlemon
---

# Local Code Review with CodeRabbit CLI

When the user wants to review their local code changes before committing or pushing, delegate to the `cr-reviewer` subagent.

## When to Use

Use this skill when the user:
- Asks to "review my code" or "check my changes"
- Mentions CodeRabbit and reviewing
- Wants to run a code review before committing
- Wants to check for issues in their changes

## What the Subagent Handles

The `cr-reviewer` subagent will:

1. **Check Prerequisites**
   - Verify CodeRabbit CLI is installed
   - Check authentication status
   - Guide through setup if needed

2. **Run Review**
   - Execute `coderabbit --prompt-only`
   - Default to `--type all` (uncommitted + committed changes)
   - Auto-detect base branch

3. **Present Results**
   - Parse and categorize findings by severity
   - Show summary with actionable items

4. **Interactive Fix Loop**
   - Apply suggested fixes
   - Re-run review to verify
   - Continue until resolved

5. **Handle Rate Limits**
   - Detect rate limit errors
   - Offer alternatives (wait, smaller changeset, manual review)

## Default Options

- **type**: `all` (both uncommitted and committed changes)
- **base**: Auto-detect from `refs/remotes/origin/HEAD`

## Example Triggers

- "Review my changes before I commit"
- "Can you check my code with coderabbit?"
- "Run a code review on what I've done"
- "Check if there are any issues in my changes"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thrownlemon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
