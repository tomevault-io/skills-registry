---
name: smart-pr
description: Generates well-structured pull request descriptions from git history. Analyzes commits, diffs, and branch context to produce clear summaries with categorized changes, testing notes, and reviewer guidance. Use when creating a PR, writing a PR description, or when the user says /smart-pr.
license: MIT
compatibility: Requires git
metadata:
  author: bmobot
  version: "1.0"
---

# Smart PR Description Generator

Generate comprehensive, well-structured pull request descriptions by analyzing your git history and changes.

## When to Activate

- User asks to create a PR description
- User says `/smart-pr` or "write a PR description"
- User is about to create a pull request and needs a description

## Instructions

### Step 1: Gather Context

Determine the base branch (default: `main`) and collect:

```bash
# Get the base branch
BASE_BRANCH="${1:-main}"

# Commits since diverging from base
git log --oneline "$BASE_BRANCH"..HEAD

# Full diff stats
git diff --stat "$BASE_BRANCH"..HEAD

# Detailed diff for analysis
git diff "$BASE_BRANCH"..HEAD
```

If there are no commits ahead of the base branch, inform the user and stop.

### Step 2: Analyze Changes

Categorize each commit by type using conventional commit patterns:

| Prefix | Category |
|--------|----------|
| `feat:` or new functionality | Features |
| `fix:` or bug repairs | Bug Fixes |
| `refactor:` or restructuring | Refactoring |
| `test:` or test changes | Testing |
| `docs:` or documentation | Documentation |
| `chore:`, `ci:`, deps | Maintenance |
| `perf:` or optimization | Performance |

For commits without conventional prefixes, infer the category from the diff content:
- New files → likely a feature
- Modified test files → testing
- Config changes → maintenance
- README/docs changes → documentation

### Step 3: Generate PR Description

Use this template:

```markdown
## Summary

[1-3 sentences describing the overall purpose and motivation]

## Changes

### [Category 1]
- [Change description with context]
- [Change description with context]

### [Category 2]
- [Change description with context]

## Files Changed

[Number] files changed, [additions] insertions(+), [deletions] deletions(-)

Key files:
- `path/to/important/file.ts` — [what changed and why]
- `path/to/another/file.ts` — [what changed and why]

## Testing

- [ ] [Specific test action based on changes]
- [ ] [Another test action]
- [ ] [Edge case to verify]

## Notes for Reviewers

[Any context that helps reviewers: design decisions, trade-offs, areas needing extra scrutiny, migration steps, etc.]
```

### Step 4: Present and Refine

1. Show the generated description to the user
2. Ask if they want to adjust anything (title, scope, details)
3. If creating the PR directly, use `gh pr create` with the description

## Quality Guidelines

- **Title**: Under 72 characters, imperative mood ("Add feature" not "Added feature")
- **Summary**: Focus on WHY, not just WHAT. Link to issues if referenced in commits.
- **Changes**: Group by category. Each bullet should explain the change AND its purpose.
- **Testing**: Be specific. "Run tests" is not helpful. "Run `npm test -- --grep auth`" is.
- **Notes**: Call out anything surprising, incomplete, or that needs follow-up.

## Examples

### Good Title
```
Add rate limiting to API endpoints
```

### Bad Title
```
Updated some files and fixed things and also added rate limiting and cleaned up tests
```

### Good Change Description
```
- Add token bucket rate limiter to /api/auth/* endpoints (100 req/min per IP)
  to prevent brute-force attacks on login
```

### Bad Change Description
```
- Updated rate-limit.ts
```

## Edge Cases

- **Single commit PR**: Use the commit message as the title, expand the body
- **Many small commits**: Group by theme rather than listing each commit
- **Merge commits**: Skip merge commits in the analysis
- **Draft PR**: Note that it's a draft and what's still TODO
- **Breaking changes**: Add a prominent "Breaking Changes" section at the top

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rockarhymellc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
