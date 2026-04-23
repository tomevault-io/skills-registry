---
name: code-review
description: Use when explicitly asked to review a git branch, Pull Request (PR), Merge Request (MR), or perform a pre-merge review. Do not use for inline code critiques.
metadata:
  author: plutowang
---

# Code Review

Perform comprehensive code reviews of a branch against the base branch, providing actionable feedback on code quality, security, performance, and best practices.

## When to Use This Skill

Activate this skill when:

- The user types "review" or "code review"
- The user types "review BRANCH-NAME" to review a specific branch
- The user asks to review a branch, pull request, or merge request
- Analyzing code changes before merging
- Performing code quality assessments
- Checking for security vulnerabilities or performance issues
- Reviewing branch diffs

**Two Review Modes:**

1. **Current Branch Review** (default when no branch specified)
   - Reviews all changes in current branch (committed + uncommitted)
   - Includes staged and unstaged changes
   - Runs automated checks (linters, formatters, tests)

2. **Other Branch Review** (when branch name specified)
   - Uses git worktree for non-disruptive review
   - Reviews only committed changes from that branch
   - Leaves your current work untouched

## Branch Selection

### Branch Name Provided

**If a branch name is provided** (e.g., "review feature/payment"):

1. Fetch latest from origin: `git fetch origin`
2. Set up a git worktree for the branch (see Worktree Setup below)
3. Proceed with the review in the worktree
4. Clean up the worktree after review is complete

### No Branch Specified (Current Branch)

**If no branch name is provided** (e.g., just "review"):

1. Review the current branch as-is in the current directory
2. **Include uncommitted changes**:
   - Staged changes: `git diff --cached`
   - Unstaged changes: `git diff`
3. **Run automated quality checks** (linters, formatters, tests)
4. Do not create a worktree or switch branches

### Worktree Setup for Non-Disruptive Reviews

When reviewing a branch that isn't the current branch, use a git worktree to avoid disturbing the current working state:

1. Create a worktree directory at `<repo-root>/.worktrees/<branch-name>`:

   ```bash
   git worktree add .worktrees/<branch-name> origin/<branch-name>
   ```

2. Perform all review operations within the worktree directory
3. After the review is complete, remove the worktree:

   ```bash
   git worktree remove .worktrees/<branch-name>
   ```

**Important**: Always use the worktree path when reading files or running git commands during the review. This ensures the user's current work remains untouched.

### Dependency Installation in Worktrees

When setting up a worktree, install dependencies if you need to run checks (tests, type checking, linting):

1. **Detect package manager**: Check for `pnpm-lock.yaml`, `Cargo.lock`, `go.mod`
2. **Install dependencies**:

   ```bash
   cd <worktree-path> && pnpm install
   ```

3. **Run checks** (optional, if needed for thorough review)

**When to install dependencies:**

- When you need to run tests, type checking, or linting
- When reviewing changes that affect build or compilation
- When the review requires verifying the code actually works

**When to skip dependency installation:**

- Simple reviews that only need to examine diffs
- Quick reviews of documentation or config changes

### Worktree Error Handling

**If the worktree already exists:**

```bash
git worktree remove .worktrees/<branch-name> --force 2>/dev/null || true
git worktree add .worktrees/<branch-name> origin/<branch-name>
```

**If no matching branch is found:**

- Inform the user that no branch was found
- List available branches that might be related (partial matches)
- Ask the user to provide the exact branch name

**Always clean up worktrees:**

- Even if the review encounters errors, attempt to clean up the worktree
- Use `git worktree list` to verify cleanup was successful
- Mention the worktree path being removed during cleanup

### .gitignore Recommendation

The `.worktrees` directory should be added to `.gitignore` if not already present. Check and suggest adding it if missing:

```bash
# Code review worktrees
.worktrees/
```

## Analyze Branch Context

First, gather essential information about the branch to review:

- Identify the current branch name (or worktree branch)
- Determine the appropriate base branch (main or master)
- Check for any uncommitted changes (current branch only)
- **Find the merge-base** to isolate only commits made in this branch
- Get the list of commits and changed files

### Detect Default Branch (Use Ancestry)

```bash
DEFAULT_BRANCH=""

for branch in main master; do
  if git merge-base --is-ancestor origin/$branch HEAD 2>/dev/null; then
    DEFAULT_BRANCH=$branch
    break
  fi
done

if [ -z "$DEFAULT_BRANCH" ]; then
  DEFAULT_BRANCH=$(git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's@^refs/remotes/origin/@@')
fi

if [ -z "$DEFAULT_BRANCH" ]; then
  if git show-ref --verify --quiet refs/remotes/origin/main; then
    DEFAULT_BRANCH="main"
  elif git show-ref --verify --quiet refs/remotes/origin/master; then
    DEFAULT_BRANCH="master"
  fi
fi

[ -z "$DEFAULT_BRANCH" ] && DEFAULT_BRANCH="main"
```

### Finding Branch-Specific Changes (CRITICAL)

**You MUST use `git merge-base` to find the common ancestor.** This ensures you only review commits that were made in THIS branch, not commits from other branches that happened to be merged into main.

```bash
MERGE_BASE=$(git merge-base origin/$DEFAULT_BRANCH HEAD)
git log --oneline $MERGE_BASE..HEAD
git diff --name-status $MERGE_BASE..HEAD
git diff $MERGE_BASE..HEAD
```

**Why this matters:**

- `git diff origin/main..HEAD` shows ALL differences between main and HEAD, which includes changes from OTHER branches that were merged into main after this branch was created
- `git diff $(git merge-base origin/main HEAD)..HEAD` shows ONLY the changes introduced in THIS branch

**Example:**

```bash
main:    A---B---C---D---E  (where D and E are from other merged branches)
              \
feature:       X---Y---Z  (this is what we want to review)

# WRONG: git diff origin/main..HEAD
# Shows: differences from E to Z (includes D and E changes we don't care about)

# CORRECT: git diff $(git merge-base origin/main HEAD)..HEAD
# Shows: only X, Y, Z changes (merge-base is B)
```

**Always use the merge-base approach for:**

- `git log` - to list commits
- `git diff` - to see changes
- `git diff --stat` - for change statistics
- `git diff --name-status` - for file list

### Uncommitted Changes (Current Branch Only)

```bash
git diff --cached --name-status
git diff --cached --stat
git diff --name-status
git diff --stat
```

### Exclude Lock Files

Do not review lock files. Filter them out:

- `pnpm-lock.yaml`
- `package-lock.json`
- `yarn.lock`
- `bun.lockb`
- `go.sum`
- `Cargo.lock`
- `poetry.lock`
- `Pipfile.lock`
- `pdm.lock`
- `Gemfile.lock`
- `composer.lock`
- `deno.lock`
- `flake.lock`

### Large Diff Confirmation

If diff is very large, ask for confirmation before proceeding:

- **Files > 100** or **Lines > 5000**

## Run Automated Quality Checks

**Current branch**: Always run checks.
**Worktree**: Ask the user before running checks (may require installing dependencies).

Run the bundled check script. It auto-detects the project type (Nx, Rust, Go, Node.js) and runs the appropriate linters, formatters, and tests with a 5-minute timeout per check. Failures are reported but do not stop the review.

```bash
./skills/global/code-review/run-checks.sh "$MERGE_BASE" [WORKTREE_PATH]
```

Capture the output and include results in the review report.

## Perform Comprehensive Code Review

Conduct a thorough review of **only the changes introduced in this branch** (using merge-base as described above).

### 1. Change Analysis

- Use `git diff $(git merge-base origin/$DEFAULT_BRANCH HEAD)..HEAD -- <file>` to review each modified file
- **If reviewing current branch**: Also review `git diff --cached` and `git diff`
- Examine commits using `git show <commit-hash>` for individual commits in the branch
- Identify patterns across changes
- Check for consistency with existing codebase
- **Only comment on code that was changed in THIS branch's commits or uncommitted work**

### 2. Code Quality Assessment

- Code style and formatting consistency
- Variable and function naming conventions
- Code organization and structure
- Adherence to DRY (Don't Repeat Yourself) principles
- Proper abstraction levels

### 3. Technical Review

- Logic correctness and edge cases
- Error handling and validation
- Performance implications
- Security considerations (input validation, SQL injection, XSS, etc.)
- Resource management (memory leaks, connection handling)
- Concurrency issues if applicable

### 4. Best Practices Check

- Design patterns usage
- SOLID principles adherence
- Testing coverage implications
- Documentation completeness
- API consistency
- Backwards compatibility

### 5. Dependencies and Integration

- New dependencies added
- Breaking changes to existing interfaces
- Impact on other parts of the system
- Database migration requirements

## Generate Review Report

Create a structured code review report with:

1. **Executive Summary**: High-level overview of changes and overall assessment
2. **Statistics**:
   - Files changed, lines added/removed
   - Commits reviewed
   - Uncommitted changes status (current branch only)
   - Critical issues found
3. **Automated Check Results**:
   - Format check: ✅ Passed / ❌ Failed
   - Linter: ✅ Passed / ⚠️ Warnings / ❌ Errors
   - Tests: ✅ Passed / ❌ Failed
   - Brief summary of failures
4. **Strengths**: What was done well
5. **Issues by Priority**:
   - 🔴 **Critical**: Must fix before merging (bugs, security issues, failed checks)
   - 🟡 **Important**: Should address (performance, maintainability)
   - 🟢 **Suggestions**: Nice to have improvements
6. **Detailed Findings**: For each issue include:
   - File and line reference
   - A question framing the concern (e.g., "Could this cause X?" or "Would it help to Y?")
   - Context explaining why you're asking
   - Code example if helpful
7. **Security Review**: Specific security considerations
8. **Performance Review**: Performance implications
9. **Testing Recommendations**: What tests should be added
10. **Documentation Needs**: What documentation should be updated

### Report Output

1. Display the complete review report in markdown format
2. Save report to `CODE_REVIEW_[YYYY-MM-DD_HH-MM-SS].md` in repo root

Example filename: `CODE_REVIEW_2026-01-27_14-30-22.md`

## User Interaction

After completing the review:

1. Display the complete review report in markdown format
2. Provide actionable next steps based on findings
3. If critical issues found, highlight them prominently
4. If a worktree was used, mention the cleanup path (e.g., `.worktrees/<branch-name>`) when removing it

## Feedback Style: Questions, Not Directives

**Frame all feedback as questions, not commands.** This encourages dialogue and respects the author's context.

### Examples

❌ **Don't write:**

- "You should use early returns here"
- "This needs error handling"
- "Extract this into a separate function"
- "Add a null check"

✅ **Do write:**

- "Could this be simplified with an early return?"
- "What happens if this API call fails? Would error handling help here?"
- "Would it make sense to extract this into its own function for reusability?"
- "Is there a scenario where this could be null? If so, how should we handle it?"

### Why Questions Work Better

- The author may have context you don't have
- Questions invite explanation rather than defensiveness
- They acknowledge uncertainty in the reviewer's understanding
- They create a conversation rather than a checklist

## Important Notes

- **CRITICAL: Only review changes from THIS branch** - use `git merge-base` to isolate branch-specific changes. Never comment on code that was changed in other branches.
- **Two review modes**: current branch includes uncommitted changes; other branches use worktrees.
- **Exclude lock files** from review (pnpm-lock.yaml, go.sum, Cargo.lock, etc.).
- **Automated checks**: Run with 5-minute timeout each using `gtimeout` or `timeout`. Continue review even if they fail.
- **Worktree cleanup**: Always remove worktree after review and mention the path being removed.
- **Frame feedback as questions** to encourage dialogue.
- **Be constructive and specific** in feedback.
- **Provide code examples** for suggested improvements.
- **Prioritize issues clearly** using severity levels.
- **Check security and performance** implications.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plutowang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
