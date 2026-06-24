---
name: git-commit
description: Generates storytelling-focused Conventional Commits messages, then commits and pushes changes. Use when the user says "commit", "git commit", or asks to commit changes, wants to create a commit, or when work is complete and ready to commit.
metadata:
  author: matthewlafalce
---

# Git Commit

Generate Conventional Commits messages that tell a complete story for future code archeology.

## When to Use This Skill

Activate this skill when:
- The user types "commit" or "git commit" (with or without slash command)
- The user says "commit this" or "let's commit"
- The user asks to create a commit message
- Work is complete and ready to commit
- The user mentions committing or pushing changes

## Critical Rules

**IMPORTANT: NEVER EVER ADD CO-AUTHOR TO THE GIT COMMIT MESSAGE**
**NEVER mention Claude Code in commit messages**
**IMPORTANT: Keep commits atomic - one logiacl change per commit**

## Workflow

### 1. Gather Context

First, collect information about the current state:

```bash
# Current git status
git status

# Current git diff (staged changes)
git diff --staged

# Recent commits for context
git log --oneline -5

# Current branch
git branch --show-current
```

### 2. Human-in-the-Loop - Ask for Context

**ALWAYS use the AskUserQuestion tool to ask WHY the change was made.**

Based on the diff, generate 3-4 plausible options for why the change was made. Present these as multiple choice options using AskUserQuestion.

**Example:**
```
Question: "Why did you make these changes?"
Options:
- "Fix bug where X was causing Y"
- "Add new feature for Z"
- "Refactor to improve maintainability"
- (User can always select "Other" to provide custom explanation)
```

The options should be specific to the actual changes observed in the diff, not generic. Analyze the code changes to infer likely motivations.

Wait for their response and incorporate their explanation into the commit message.

### 3. Analyze Technical Changes

Review the staged changes to understand WHAT changed technically:
- Files modified
- Functions added/updated
- Dependencies changed
- Configuration updates

### 4. Create Enhanced Commit Message

Generate a commit message that tells a complete story for future code archeology:

**Format:**
```
type(scope): concise subject line describing what changed

Why this change was needed:
[Incorporate the user's explanation]

What changed:
[Technical summary of the modifications]

Problem solved:
[Explain the business/technical problem this addresses]
```

**Conventional Commits Types:**
- **FEAT**: new features
- **FIX**: bug fixes
- **DOCS**: documentation changes
- **STYLE**: formatting, missing semicolons, etc.
- **REFACTOR**: code restructuring without changing functionality
- **TEST**: adding or updating tests
- **CHORE**: maintenance tasks, dependencies, build process
- **PERF**: performance improvements
- **CI**: continuous integration changes

### 5. Execute Commit and Push (Requires Confirmation)

**IMPORTANT: Do not use `git add -A` or `git add .`**
Commit only the files that are already staged and understood.

**CRITICAL: The user MUST confirm before executing `git commit` or `git push`.**
These commands are intentionally NOT in the allowed-tools list, so the user will be prompted for approval.

After receiving the user's approval of the commit message:

1. **Commit with heredoc** (for multi-line messages):
```bash
git commit -m "$(cat <<'EOF'
type(scope): subject line

Why this change was needed:
[explanation]

What changed:
[technical summary]

Problem solved:
[problem description]

EOF
)"
```

2. **Push**:
```bash
git push
```

## Storytelling Emphasis

Create commit messages that future developers will appreciate when doing code archeology months later. The message should answer:

- **What** changed? (technical summary)
- **Why** was this needed? (business context, user explanation)
- **What problem** does it solve? (from user input)

## Examples

### Example 1: Bug Fix

```
FIX(auth): prevent token refresh race condition

Why this change was needed:
Multiple simultaneous requests were triggering concurrent token refresh
attempts, causing some requests to fail with stale tokens.

What changed:
- Added mutex lock around token refresh logic
- Implemented token refresh deduplication
- Added retry logic for failed requests during refresh

Problem solved:
Concurrent requests no longer cause authentication failures due to
token refresh race conditions.
```

### Example 2: Refactoring

```
REFACTOR(api): extract validation logic to shared module

Why this change was needed:
Input validation was duplicated across 7 different API endpoints,
making it difficult to maintain consistent validation rules.

What changed:
- Created shared validation utilities in packages/validation
- Updated all API endpoints to use shared validators
- Removed 200+ lines of duplicated validation code

Problem solved:
Validation logic is now centralized and consistent across all endpoints.
Future validation changes only need to be made in one place.
```

## Important Notes

- **Never skip the "why" question** - User context is crucial
- **Use heredoc for multi-line commits** - Ensures proper formatting
- **Be specific** in technical summaries
- **Think about the reader** - someone debugging this code in 6 months
- **No co-authors** - Never add "Co-Authored-By" or mention Claude Code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matthewlafalce) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
