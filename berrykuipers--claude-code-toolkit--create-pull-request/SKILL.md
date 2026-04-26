---
name: create-pull-request
description: Create GitHub pull requests with proper issue linking, comprehensive descriptions, and quality metrics for WescoBar project Use when this capability is needed.
metadata:
  author: berrykuipers
---

# Create Pull Request

## Purpose

Create well-structured pull requests with proper GitHub issue linking, comprehensive descriptions, quality metrics, and adherence to WescoBar project standards.

## When to Use

- After completing feature implementation and all quality gates pass
- During Conductor workflow Phase 4 (PR Creation)
- When ready to submit code for review
- After all tests pass and audit score ≥ 8.0

## Critical Requirements

### ✅ MUST Do Before PR Creation

1. **All tests passing** - No failing tests allowed
2. **Audit score ≥ 8.0** - Quality threshold met
3. **Build successful** - Production build completes
4. **Commits pushed** - All commits on remote branch
5. **Branch up-to-date** - Synced with base branch (development)

### ❌ NEVER Do

- Create PR with failing tests
- Skip quality gates
- Use incorrect issue linking format
- Create PR before all validation passes

## Instructions

### Step 1: Gather PR Metadata

Collect required information:
- **Issue number**: From branch name or conductor context
- **Issue title**: From GitHub issue
- **Files changed**: Count from git diff
- **Test coverage**: From test results
- **Audit score**: From audit agent
- **Implementation summary**: Key changes made

### Step 2: Draft PR Body

Use this template:

```markdown
## Summary
[Brief description of what was implemented]

## Changes
- [Key change 1]
- [Key change 2]
- [Key change 3]

## Architecture Review
- VSA compliance: ✅
- SOLID principles: ✅
- Layer boundaries: ✅

## Test Coverage
- Unit tests: [COVERAGE]%
- Integration tests: ✅ Passing
- UI tests: ✅ Passing
- E2E tests: ✅ Passing [if applicable]

## Quality Metrics
- Audit score: [SCORE]/10
- Build: ✅ Passing
- Lint: ✅ Clean
- TypeScript: ✅ No errors

## Files Changed
- Modified: [COUNT] files
- Created: [COUNT] files
- Deleted: [COUNT] files

## Issue Reference
Fixes #[ISSUE_NUMBER]

🤖 Generated with [Claude Code](https://claude.ai/code)

Co-Authored-By: Claude <noreply@anthropic.com>
```

### Step 3: Validate Issue Linking Format

**CRITICAL**: Issue linking MUST use exact format `Fixes #123`

✅ **CORRECT:**
- `Fixes #123`
- `Closes #456`
- `Resolves #789`

❌ **WRONG (GitHub won't auto-close):**
- `Fixes: #123` (colon breaks it)
- `**Fixes:** #123` (markdown breaks it)
- `Fix #123` (singular doesn't work)
- `fixes #123` (lowercase doesn't work in PR body)

### Step 4: Create PR with gh CLI

```bash
# Create PR with proper base and head branches
gh pr create \
  --title "feat: [FEATURE_TITLE]" \
  --body "[PR_BODY_FROM_STEP_2]" \
  --base development \
  --head [BRANCH_NAME]
```

### Step 5: Verify PR Creation

After creation:
1. ✅ PR URL returned
2. ✅ PR number assigned
3. ✅ Issue linked (check on GitHub)
4. ✅ All checks queued/running

## PR Title Convention

Follow conventional commits:

```
feat: Add user dark mode toggle
fix: Resolve character portrait caching issue
refactor: Simplify WorldContext state management
test: Add integration tests for Gemini API
docs: Update README with new API patterns
```

## Common Issues

### Issue: PR creation fails with "No commits between base and head"
**Solution**: Ensure commits are pushed to remote branch:
```bash
git push -u origin [BRANCH_NAME]
```

### Issue: Issue doesn't auto-link
**Solution**: Check issue linking format - must be `Fixes #123` (exact format)

### Issue: PR checks don't start
**Solution**: Verify GitHub Actions are enabled for repository

## Integration with Conductor Workflow

The Conductor agent uses this skill in Phase 4:

```markdown
**Phase 4, Step 3**: Create Pull Request

Using the `create-pull-request` skill:
- Gather all metrics from previous phases
- Draft comprehensive PR body
- Validate issue linking format
- Create PR with gh CLI
- Verify creation successful
```

## Related Skills

- `commit-changes` - Single atomic commit before PR
- `link-github-issue` - Validate issue linking format
- `monitor-ci-checks` - Monitor PR checks after creation

## Additional Resources

See `REFERENCE.md` in this skill directory for:
- Complete PR template examples
- Issue linking format reference
- GitHub CLI documentation
- Troubleshooting guide

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/berrykuipers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
