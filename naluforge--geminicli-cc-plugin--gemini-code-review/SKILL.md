---
name: gemini-code-review
description: >- Use when this capability is needed.
metadata:
  author: NaluForge
---

# Code Review Cross-Check with Gemini

**Invoke using `/gemini-review` or `mcp__gemini__gemini_execute` with a review-focused prompt.**

## When to Use Gemini for Review

Gemini provides value as an independent reviewer when:
- Changes are large (>500 lines) and benefit from fresh eyes
- Changes touch security-sensitive code (auth, crypto, input validation)
- You want to catch bugs that familiarity with the code might mask
- A PR needs review and no human reviewer is immediately available
- You want a second opinion on architectural decisions in the diff

## Gathering Context

Good reviews need full context, not just diffs:

1. **Get the diff**: `git diff` for working changes, `gh pr diff N` for PRs
2. **Get full file content**: Changed lines make more sense with surrounding code.
   Read the complete files, not just the diff hunks.
3. **Get commit history**: `git log main..HEAD --oneline` shows the intent
   behind the changes
4. **Get PR description**: `gh pr view N --json title,body` for context on
   what the PR aims to accomplish

## Structuring the Review Prompt

Send Gemini a structured review request:
- The full diff
- Complete content of changed files
- PR description or commit messages for intent
- Specific areas of concern (if any)

Ask Gemini to check for:
- **Bugs**: Logic errors, off-by-one, null/undefined handling
- **Security**: Injection, auth bypasses, data exposure, secrets in code
- **Edge cases**: Empty inputs, concurrent access, error paths
- **Performance**: N+1 queries, unnecessary allocations, missing indexes
- **Consistency**: Naming conventions, patterns used elsewhere in the codebase

## Parameters

- **model**: Prefer `pro` for large or security-sensitive reviews; `flash` is sufficient for small diffs
- **timeout_ms**: `2400000` (40 minutes) — large diffs and full-repo reviews need extended time
- **include_directories**: Directories containing the changed files

## Interpreting Results

Group findings by severity:
- **Critical**: Must fix before merging (bugs, security, data loss)
- **Warning**: Should investigate (logic concerns, missing error handling)
- **Suggestion**: Nice to have (style, naming, documentation)

A clean review (no findings) is valuable signal — it means an independent
model found nothing concerning. Report this explicitly rather than staying
silent.

---
> Source: [NaluForge/geminicli-cc-plugin](https://github.com/NaluForge/geminicli-cc-plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
