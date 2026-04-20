---
name: pr-attribution
description: Shows who will be attributed to the changes in your PR by analyzing commit authors and line-by-line changes. Use when the user asks who will be credited for changes, who authored the PR changes, or when syncing with main causes unexpected authors to appear. Use when this capability is needed.
metadata:
  author: zmchenry
---

# PR Attribution

This skill analyzes the authorship of changes in your current branch to show who will be attributed to the changes when you create a PR. This is especially useful after rebasing or merging with main, when other developers' commits might appear in your branch.

## Quick Start

When the user asks about PR attribution:

1. Identify the base branch (usually main)
2. Show all commits that will be in the PR
3. Analyze commit authorship
4. Show line-by-line change attribution
5. Provide summary and explanation

## Instructions

### Step 1: Get branch information

```bash
# Get current branch name
git branch --show-current

# Get configured user info
git config user.name
git config user.email
```

### Step 2: Analyze commit authorship

```bash
# Show all commits that will be in the PR with author info
git log main..HEAD --pretty=format:"%h - %an <%ae> - %ar - %s" --date=relative

# Get commit count by author
git log main..HEAD --pretty=format:"%an <%ae>" | sort | uniq -c | sort -rn
```

This shows:
- Each commit that will be in the PR
- Who authored each commit
- When it was authored
- Commit message

### Step 3: Analyze line-by-line changes

For each file that has been modified, show who authored the current state of the changed lines:

```bash
# Get list of changed files
git diff --name-only main...HEAD

# For each changed file, show the blame information for changed lines
# This requires analyzing the diff and running git blame on those line ranges
```

**Detailed process:**

1. Get the diff with line numbers:
```bash
git diff main...HEAD --unified=0 [filename]
```

2. For files with changes, show who last modified those lines in the current branch:
```bash
git blame [filename] -L[start],[end] --date=short
```

3. Compare with who modified those lines in main:
```bash
git blame main -- [filename] -L[start],[end] --date=short 2>/dev/null || echo "New file or lines"
```

### Step 4: Identify unexpected authors

Analyze the data to identify potential issues:

**Check for:**
- Commits from other authors in your branch
- Changed lines last modified by other authors
- Files created or modified by others appearing in your diff

**Common causes:**
1. **Rebasing on main**: When you rebase, you replay your commits on top of main. If files you modified were also changed in main, the newer changes from main become the base.

2. **Merge conflicts resolved**: When resolving merge conflicts, you might keep other developers' code, which shows as your changes in the diff but their authorship in the blame.

3. **Cherry-picked commits**: If you cherry-picked commits from other branches, those commits retain original authorship.

4. **Branch created from wrong base**: If your branch was created from another feature branch instead of main.

### Step 5: Provide clear summary

Present the findings in a clear format:

**Your commits:** (commits where you are the author)
```
- abc1234 - Your Name <your@email> - 2 hours ago - Add feature X
- def5678 - Your Name <your@email> - 1 hour ago - Fix bug Y
```

**Other developers' commits in your branch:**
```
- ghi9012 - Other Dev <other@email> - 3 hours ago - Update shared utility
```

**Files you're changing:**
Show each file with:
- Total lines added/removed
- Your changes vs existing code authorship
- Any lines where you're modifying other developers' recent work

**Summary:**
- Total commits: X (Y by you, Z by others)
- Files modified: X
- Lines added: X (by you)
- Lines removed: X
- Lines modified that were authored by others: X

### Step 6: Explain implications

Based on the findings, explain:

**If all commits are yours:**
"All commits in this PR will be attributed to you. The changes are yours."

**If other developers' commits appear:**
"Your branch includes commits from other developers:
- [List commits and authors]

This typically happens because:
- You rebased on main after they merged their changes
- You merged main into your branch
- These commits will appear in your PR but are attributed to them

**Action:** Consider whether you want to:
1. Keep it as-is (normal for rebased branches)
2. Squash commits to consolidate authorship
3. Verify these changes are meant to be in your PR"

**If you're modifying other developers' recent code:**
"You're modifying lines that were recently changed by:
- [List files and authors]

This is normal if:
- You're building on their changes
- You're fixing issues in shared code
- You resolved merge conflicts

The PR will show these as your changes, which is correct since you modified them."

## Output Format

Present the information in this structure:

### Commit Attribution
```
Your commits (X):
- [hash] - [message] - [time ago]

Other developers' commits (Y):
- [hash] - [author] - [message] - [time ago]
```

### File Changes
```
File: path/to/file.py
  +50 -20 lines
  - 40 lines newly added by you
  - 15 lines modified (originally by [Author])
  - 15 lines removed

File: path/to/other.py
  +10 -5 lines
  - All changes by you
```

### Summary
- **Your commits:** X
- **Other commits in branch:** Y
- **Total files changed:** Z
- **Net lines by you:** +X -Y
- **Modifying other developers' recent code:** [Yes/No]

### Explanation
[Clear explanation of the situation and any actions to consider]

## Advanced Analysis

### Check for accidental inclusions

Look for signs that commits might be accidentally included:

```bash
# Check if branch diverged from main or another branch
git merge-base --fork-point main

# Show the actual diff that will be in the PR (excluding other commits)
git diff main...HEAD

# Find commits that exist in your branch but not from your fork point
git log --oneline --graph --decorate main..HEAD
```

### Identify rebase artifacts

After a rebase, check if the commit history looks as expected:

```bash
# Show commits with both author and committer info
git log main..HEAD --pretty=format:"%h - Author: %an <%ae> - Committer: %cn <%ce> - %s"
```

Note: After a rebase:
- **Author** = original commit author (stays the same)
- **Committer** = person who rebased (changes to you)

### Compare with remote

If the branch was previously pushed:

```bash
# Show what's different from the remote version
git log origin/$(git branch --show-current)..HEAD --oneline 2>/dev/null || echo "Not pushed yet"

# Show if force-push would be needed
git status -sb
```

## Common Scenarios

### Scenario 1: Clean feature branch
```
Current branch: feature/add-login
Your commits: 3
Other commits: 0
All changes authored by you
→ "All changes will be attributed to you ✓"
```

### Scenario 2: Rebased on updated main
```
Current branch: feature/add-login
Your commits: 3
Other commits: 5 (from main)
Changed files overlap with others' work
→ "Your branch includes 5 commits from other developers that were merged to main.
   This is normal after rebasing. These commits are attributed to their original authors.
   Your 3 commits are attributed to you."
```

### Scenario 3: Possible accident
```
Current branch: feature/add-login
Your commits: 2
Other commits: 8 (from developer X's feature branch)
Many files modified by developer X
→ "⚠️ Your branch includes 8 commits from Developer X that aren't in main yet.
   This suggests your branch may have been created from their feature branch.
   Review if these changes should be in your PR."
```

## Edge Cases

### Co-authored commits

If you want to share attribution with another developer:

```bash
git commit -m "Commit message

Co-authored-by: Other Dev <other@email.com>"
```

### Amending commits

If you need to fix authorship:

```bash
# Change author of last commit
git commit --amend --author="Your Name <your@email.com>"

# Change author of older commits (use with caution)
git rebase -i main
# Mark commits as 'edit', then amend each one
```

## Important Notes

- Run this analysis before creating a PR to verify attribution
- Other developers' commits in your branch isn't necessarily wrong
- Focus on whether the PR diff makes sense, not just commit list
- When in doubt, check with your team about the expected workflow
- Consider using `git log --first-parent` to see merge commits more clearly

## Related Commands

The user might also want to:
- See the actual diff: `git diff main...HEAD`
- See the commit graph: `git log --graph --oneline main..HEAD`
- Check PR on GitHub: `gh pr view` (if PR already exists)
- Interactive rebase: `git rebase -i main` (to modify commits)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zmchenry) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
