---
name: smart-commit
description: Generates clear, conventional commit messages from staged changes. Analyzes diffs to produce focused commit messages that explain WHY, not just WHAT. Supports conventional commits, multi-file changes, and interactive refinement. Use when committing code or when the user says /smart-commit.
license: MIT
compatibility: Requires git
metadata:
  author: bmobot
  version: "1.0"
---

# Smart Commit Message Generator

Generate clear, well-structured commit messages by analyzing your staged changes.

## When to Activate

- User asks to commit with a good message
- User says `/smart-commit` or "commit this" or "write a commit message"
- User has staged changes and wants help describing them

## Instructions

### Step 1: Check Staged Changes

```bash
# What's staged?
git diff --cached --stat

# Detailed diff of staged changes
git diff --cached

# Any unstaged changes the user might want to include?
git diff --stat
```

If nothing is staged:
1. Show unstaged changes with `git diff --stat`
2. Ask the user what to stage, or suggest `git add -p` for selective staging
3. Stop until changes are staged

### Step 2: Analyze the Diff

Read the staged diff and determine:

1. **Type**: What kind of change is this?
   - `feat` — New functionality
   - `fix` — Bug repair
   - `refactor` — Restructuring without behavior change
   - `perf` — Performance improvement
   - `test` — Adding or fixing tests
   - `docs` — Documentation only
   - `chore` — Build, CI, dependencies, config
   - `style` — Formatting, whitespace (no logic change)

2. **Scope** (optional): What area of the codebase?
   - Module name, component, feature area
   - Only include if the project uses scoped commits

3. **Subject**: One line summarizing the change
   - Imperative mood: "Add feature" not "Added feature"
   - Under 50 characters (hard limit: 72)
   - Lowercase first word (after type prefix)
   - No period at the end

4. **Body** (if needed): Explain WHY, not WHAT
   - The diff shows WHAT changed — the message should explain WHY
   - Include motivation, context, or trade-offs
   - Wrap at 72 characters
   - Separate from subject with blank line

5. **Footer** (if applicable):
   - `BREAKING CHANGE:` for breaking changes
   - `Closes #123` for issue references
   - `Co-authored-by:` for pair programming

### Step 3: Check Recent History

```bash
# Match the project's commit style
git log --oneline -10
```

Adapt your message format to match the project's existing conventions:
- Does the project use conventional commits (`feat:`, `fix:`)?
- Does it use scopes (`feat(auth):`)?
- Does it use ticket numbers (`[PROJ-123]`)?
- What tense and style do recent messages use?

### Step 4: Generate Message

**For simple, single-purpose changes:**

```
feat: add rate limiting to auth endpoints
```

**For changes that need context:**

```
fix: prevent duplicate webhook deliveries

The retry logic was not checking if a webhook had already been
successfully delivered before retrying. This caused duplicate
notifications when the initial response was slow but successful.

Adds a delivery receipt check before each retry attempt.
Closes #847
```

**For multi-file changes with a single theme:**

```
refactor: extract email validation into shared utility

Three endpoints were each implementing their own email regex.
Consolidates into a single validateEmail() function with
proper RFC 5322 compliance and unit tests.
```

### Step 5: Present and Execute

1. Show the proposed commit message
2. Ask if the user wants to adjust it
3. Execute the commit:

```bash
git commit -m "$(cat <<'EOF'
<commit message here>
EOF
)"
```

## Commit Quality Rules

### Good Commits
- **Atomic**: One logical change per commit
- **Complete**: The codebase works after this commit
- **Explained**: The message tells you WHY without reading the diff
- **Findable**: `git log --grep` can find it by keyword

### Signs of a Bad Commit
- "Fix stuff" / "WIP" / "misc changes" — too vague
- "Update auth.ts, user.ts, config.ts, test.ts" — describes files, not purpose
- 500+ lines touching 3 unrelated features — should be split
- "Add feature and also fix that bug and update deps" — multiple purposes

### When to Suggest Splitting

If the staged changes contain multiple unrelated changes, suggest splitting:

```
I see two separate concerns in these changes:
1. Rate limiting logic (auth.ts, middleware.ts)
2. Typo fix in README

Want me to help split these into two commits? I'd suggest:
- `git reset HEAD README.md` to unstage the README
- Commit the rate limiting changes first
- Then stage and commit the README fix separately
```

## Edge Cases

- **Empty commit message request**: Never create an empty message. Analyze the diff.
- **Merge commits**: Use the default merge message unless the user asks otherwise.
- **Amend requests**: Use `git commit --amend` but warn about rewriting published history.
- **Fixup commits**: Support `git commit --fixup=<hash>` for changes that will be squashed.
- **Co-authored work**: Include `Co-authored-by:` footer when the user mentions pair programming.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rockarhymellc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
