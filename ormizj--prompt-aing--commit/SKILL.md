---
name: commit
description: Creates well-formatted git commits by analyzing staged changes and matching repository commit style. Use when user asks to commit changes, create a commit, or save work to git. Requires git repository.
metadata:
  author: ormizj
---

# Git Commit Assistant

This skill helps create meaningful, well-formatted git commits by analyzing your changes and matching your repository's commit style.

## Quick start

When invoked, automatically:
1. Check git status and staged changes
2. Analyze recent commit history for style patterns
3. Generate a commit message that matches the repo's conventions
4. Present the message for user approval
5. Execute the commit

## Workflow

### 1. Analyze Current State (Parallel Execution)

Run these git commands simultaneously:

```bash
git status                          # See staged/unstaged files
git diff --staged                   # View staged changes
git log -10 --oneline              # Recent commit messages for style
```

### 2. Generate Commit Message

Based on the analysis:

- **Identify change type**:
  - `feat:` - New feature
  - `fix:` - Bug fix
  - `refactor:` - Code restructuring
  - `docs:` - Documentation
  - `style:` - Formatting, whitespace
  - `test:` - Tests
  - `chore:` - Build, dependencies, tooling
  - `perf:` - Performance improvements

- **Match existing style**:
  - If repo uses conventional commits → use that format
  - If repo uses custom format → match it exactly
  - Check capitalization, punctuation, length patterns

- **Write concise summary**:
  - 50 characters or less for first line
  - Present tense, imperative mood ("add" not "added")
  - No period at the end
  - Focus on WHAT and WHY, not HOW

- **Add detailed body if needed**:
  - Blank line after summary
  - Wrap at 72 characters
  - Explain motivation, context, side effects

### 3. Present to User

Show the generated commit message with context:

```
📋 Proposed commit message:
────────────────────────────────
[generated message here]
────────────────────────────────

Based on:
- [X] files changed
- Changes: [brief summary]
- Style: [detected pattern]

Ready to commit? (You can also provide your own message)
```

### 4. Execute Commit

If user approves or provides their own message:

```bash
git commit -m "message line 1" -m "body line 1" -m "body line 2"
```

For multi-line messages, use multiple `-m` flags or a heredoc:

```bash
git commit -m "$(cat <<'EOF'
feat: add user authentication

Implements JWT-based authentication with refresh tokens.
Includes login, logout, and token refresh endpoints.
EOF
)"
```

## Handling Edge Cases

### No staged changes
If `git diff --staged` is empty:
- Inform user no changes are staged
- Show unstaged files with `git status`
- Ask if they want to stage files first
- DO NOT create an empty commit

### Large changesets
If many files are changed:
- Group related changes in commit message
- Suggest splitting into multiple commits if changes are unrelated
- Focus on the primary purpose of the commit

### User provides custom message
If user says "commit with message X":
- Skip analysis and use their message directly
- Still validate that changes are staged
- Confirm before committing

### Amending commits
If user wants to amend:
```bash
git commit --amend -m "new message"
```
Warn if commit has been pushed to remote.

### Commit hooks fail
If pre-commit hooks fail:
- Show the hook output
- Explain what failed
- Suggest fixes (linting, tests, etc.)
- Re-run commit after fixes

## Message Quality Guidelines

**Good commit messages:**
- ✅ `feat: add password reset flow`
- ✅ `fix: prevent memory leak in event listeners`
- ✅ `refactor: extract validation logic to utils`
- ✅ `docs: update API authentication guide`

**Poor commit messages:**
- ❌ `update files` (too vague)
- ❌ `fixed bug` (what bug?)
- ❌ `WIP` (not descriptive)
- ❌ `asdfasdf` (meaningless)

## Requirements

- Must be in a git repository
- Git must be installed and accessible
- User must have files staged for commit (or be prompted to stage them)

## Response Format

Always be conversational and helpful:
- Explain what you're analyzing
- Show your reasoning for the commit message
- Give the user control over the final message
- Confirm successful commit with short hash and message

Example interaction:
```
I'll analyze your staged changes and create a commit message.

[runs git commands]

I can see you've:
- Modified app/components/AccordionItem.vue (added expand/collapse logic)
- Updated app/pages/index.vue (integrated new accordion)

Your recent commits use conventional format with lowercase types.

I suggest:
  feat: add accordion component with expand/collapse

This matches your repo's style and clearly describes the feature.
Ready to commit with this message?
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ormizj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
