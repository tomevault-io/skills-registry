---
name: smart-commit
description: Analyze git diffs and intelligently split mixed changes into clean, logical conventional commits. Use when the user needs to organize uncommitted changes that mix multiple change types (feat, fix, refactor, chore, docs, etc.) into separate semantic commits. Handles partial file staging when a single file contains multiple change types. Triggered by requests like "split my changes into proper commits", "create semantic commits", or "organize into conventional commits". Use when this capability is needed.
metadata:
  author: jiezhuzzz
---

# Smart Commit

Intelligently analyze git diffs and split mixed changes into clean, logical conventional commits using the standard format: `type(scope): description`.

## Workflow

Follow this systematic approach to create semantic commits from mixed changes:

### 1. Analyze Current State

First, understand what changes exist:

```bash
# Check repository status
git status

# View all unstaged changes
git diff

# View all staged changes (if any)
git diff --staged

# View both staged and unstaged changes
git diff HEAD
```

Determine which changes exist and whether they're staged or unstaged.

### 2. Analyze and Categorize Changes

Examine the diff output and categorize each change by:

**Change Type** - See [references/commit-types.md](references/commit-types.md) for detailed guidance:
- `feat`: New features or functionality
- `fix`: Bug fixes
- `refactor`: Code restructuring without behavior change
- `chore`: Maintenance tasks (deps, config, etc.)
- `docs`: Documentation changes
- `style`: Formatting, whitespace, missing semicolons
- `test`: Adding or updating tests
- `perf`: Performance improvements
- `ci`: CI/CD configuration changes
- `build`: Build system or external dependencies

**Scope** - The area affected (optional but recommended):
- Examples: `auth`, `api`, `ui`, `database`, `config`

**File-level Analysis** - For each file, identify:
- Which lines/hunks belong to which change type
- Whether the file contains a single change type (simple) or multiple types (needs splitting)

### 3. Create Commit Plan

Present a clear commit plan to the user showing:

1. **Commit sequence** - Logical order of commits
2. **For each commit:**
   - Commit message: `type(scope): description`
   - Files affected and what changes will be included
   - For files with mixed changes: specify which lines/hunks will be staged

**Example format:**
```
Proposed commit plan:

Commit 1: feat(auth): add login endpoint
- src/auth.js (lines 10-25: new login function)
- src/routes.js (full file: new route registration)

Commit 2: refactor(auth): rename authentication variables
- src/auth.js (lines 40-55: variable renaming)
- src/config.js (full file: config variable updates)

Commit 3: docs(auth): fix typo in authentication comments
- src/auth.js (line 70: comment fix)

Does this plan look good?
```

**Ordering principle**: Commit in logical order - typically feat → fix → refactor → chore → docs/style. This makes the history easier to understand.

### 4. Get User Approval

Wait for explicit user approval before executing the plan. Users may request adjustments to:
- Commit messages
- Which changes are grouped together
- The order of commits
- Scope definitions

### 5. Execute Commits

Once approved, execute the commits using git's partial staging capabilities:

**For files with single change type:**
```bash
git add path/to/file
git commit -m "type(scope): description"
```

**For files with mixed changes:**

Use interactive staging to stage only relevant portions:

**Option A: Patch mode (when changes are in separate hunks):**
```bash
# Review each hunk interactively
git add -p path/to/file
# For each hunk:
# - y: stage this hunk
# - n: skip this hunk
# - s: split hunk into smaller hunks
# - e: manually edit the hunk
# - q: quit (don't stage remaining hunks)

git commit -m "type(scope): description"
```

**Option B: Manual edit (when changes are interleaved):**
```bash
# Opens diff in editor to manually select lines
git add -e path/to/file
# In the editor:
# - Delete lines you DON'T want to stage (lines starting with +/-)
# - Keep lines you DO want to stage
# - Save and exit

git commit -m "type(scope): description"
```

**Important notes:**
- After each commit, verify what's left: `git status` and `git diff`
- Continue with the next commit in the sequence
- If using `git add -p` or `git add -e`, you may need to run them multiple times for the same file across different commits

### 6. Verify Results

After all commits are created:

```bash
# Review commit history
git log --oneline -n <number-of-commits>

# Review each commit's changes
git show HEAD    # most recent commit
git show HEAD~1  # previous commit
git show HEAD~2  # two commits ago
```

Confirm all changes have been committed and the history looks clean.

## Common Scenarios

**Scenario 1: Multiple files, each with single change type**
- Straightforward: `git add` each file and commit

**Scenario 2: One file with multiple change types in separate areas**
- Use `git add -p` to stage hunks individually

**Scenario 3: One file with interleaved changes**
- Use `git add -e` for precise line-by-line control

**Scenario 4: Mix of simple and complex files**
- Handle simple files first with full `git add`
- Use partial staging for complex files

## Guidelines

- **Be thorough**: Analyze every line to ensure proper categorization
- **Communicate clearly**: Show the user exactly what will be committed in each step
- **Stay atomic**: Each commit should represent one logical change
- **Follow conventions**: Use standard conventional commit format consistently
- **Verify continuously**: Check status and diff after each commit to track progress
- **Handle errors gracefully**: If staging fails, explain what happened and adjust approach

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jiezhuzzz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
