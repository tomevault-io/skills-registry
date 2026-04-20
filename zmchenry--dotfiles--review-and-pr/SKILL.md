---
name: review-and-pr
description: Performs a code review of the current branch changes and creates a PR if the code passes review, otherwise provides a task list for improvements. Use when the user asks to review code and create a PR, or to check if code is ready for PR. Use when this capability is needed.
metadata:
  author: zmchenry
---

# Review and PR

This skill reviews code changes in the current branch and either creates a pull request if the code looks good, or provides actionable improvement tasks if issues are found.

## Quick Start

When the user asks to review and create a PR:

1. Analyze all changes in the current branch
2. Run automated checks (linting, type checking, tests)
3. Review code quality and completeness
4. Either create the PR or provide improvement tasks

## Instructions

### Step 1: Gather branch information

```bash
# Get current branch name
git branch --show-current

# Get the base branch (usually main)
git remote show origin | grep "HEAD branch"

# Check if branch is pushed to remote
git rev-parse --abbrev-ref --symbolic-full-name @{u} 2>/dev/null || echo "Not tracking remote"

# Get list of changed files
git diff --name-only main...HEAD

# Get full diff to understand changes
git diff main...HEAD
```

### Step 2: Run automated checks

Run all applicable automated checks based on the project type:

**Python projects:**
```bash
# Check for mypy errors
clyde lint mypy api 2>&1 || true

# Check for other linting issues if available
# Look for .flake8, .pylintrc, pyproject.toml
```

**JavaScript/TypeScript projects:**
```bash
# Run ESLint if available
npm run lint 2>&1 || npx eslint . 2>&1 || true

# Run type checking
npm run type-check 2>&1 || npx tsc --noEmit 2>&1 || true
```

**Run tests:**
```bash
# Python
pytest 2>&1 || python -m pytest 2>&1 || true

# JavaScript/TypeScript
npm test 2>&1 || npm run test 2>&1 || true
```

**Check git status:**
```bash
git status
```

### Step 3: Code quality review

Analyze the code changes for:

**Completeness:**
- Are there TODOs or FIXME comments that should be addressed?
- Are there incomplete implementations?
- Are there commented-out code blocks that should be removed?

**Code quality:**
- Is the code following project conventions?
- Are variable/function names clear and descriptive?
- Is there unnecessary code duplication?
- Are there overly complex functions that need refactoring?

**Testing:**
- Are there tests for new functionality?
- Do existing tests pass?
- Is test coverage adequate for the changes?

**Documentation:**
- Are complex functions documented?
- Are there necessary README updates?
- Are breaking changes documented?

**Security & Best Practices:**
- Are there hardcoded credentials or secrets?
- Are there SQL injection or XSS vulnerabilities?
- Are error cases handled properly?
- Are there race conditions or concurrency issues?

### Step 4: Make decision

Based on the review, categorize issues by severity:

**Critical issues** (blocks PR):
- Failing tests
- Critical linting/type errors
- Security vulnerabilities
- Syntax errors or code that won't compile
- Hardcoded secrets or credentials

**Major issues** (blocks PR):
- Missing tests for new functionality
- Significant code quality problems
- Incomplete implementations
- Breaking changes without documentation

**Minor issues** (create PR with note):
- Minor linting warnings
- Code style inconsistencies
- Missing or incomplete comments
- Small refactoring opportunities

**Decision logic:**
- If there are Critical or Major issues → Provide task list for improvement
- If only Minor issues or no issues → Create PR (mention minor issues in description)

### Step 5a: If issues found - Create task list

Use the TodoWrite tool to create a task list with all the issues found:

```
TodoWrite with tasks like:
- Fix mypy error in module/file.py:123
- Add tests for new feature X
- Remove hardcoded API key in config.py
- Refactor complex function in service.py
- Update README with new configuration options
```

Inform the user:
"I found [X] issues that should be addressed before creating the PR. Here's a task list to guide the improvements. Once these are resolved, I can create the PR."

### Step 5b: If code looks good - Create PR

If the code passes review:

1. Ensure the branch is pushed:
```bash
# Check remote tracking
git rev-parse --abbrev-ref --symbolic-full-name @{u} 2>/dev/null || git push -u origin $(git branch --show-current)

# Push if needed
git push 2>&1 || true
```

2. Get commit history for PR description:
```bash
git log main..HEAD --oneline
git diff main...HEAD --stat
```

3. Create the PR using gh CLI:
```bash
gh pr create --title "Your PR Title" --body "$(cat <<'EOF'
## Summary
[Describe what this PR does]

## Changes
- [Key change 1]
- [Key change 2]

## Testing
[How this was tested]

## Notes
[Any minor issues or follow-up items]

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

4. Return the PR URL to the user

## Review Criteria

### Critical Issues (Block PR)
- ❌ Failing tests
- ❌ Critical type/lint errors
- ❌ Security vulnerabilities
- ❌ Hardcoded secrets
- ❌ Syntax errors

### Major Issues (Block PR)
- ⚠️ Missing tests for new features
- ⚠️ Incomplete implementations
- ⚠️ Significant code quality problems
- ⚠️ Breaking changes without docs

### Minor Issues (Note in PR)
- ℹ️ Minor lint warnings
- ℹ️ Style inconsistencies
- ℹ️ Missing comments
- ℹ️ Small refactoring opportunities

## Example Workflows

### Scenario 1: Code is ready
```
User: "Review my changes and create a PR"
→ Run checks: All pass ✓
→ Review code: Minor style issues only
→ Create PR with note about style issues
→ Return PR URL
```

### Scenario 2: Issues found
```
User: "Review my changes and create a PR"
→ Run checks: 3 mypy errors, 2 failing tests ✗
→ Review code: Missing test coverage
→ Create task list with 5 improvement tasks
→ Explain what needs fixing
```

## Important Notes

- Always run automated checks before manual review
- Be thorough but pragmatic - perfect is the enemy of done
- Prioritize issues that affect functionality over style
- Include minor issues as notes in PR rather than blocking
- Always verify tests pass before creating PR
- Check for uncommitted changes before reviewing

## Customization

Projects may have specific requirements. Look for:
- `.github/PULL_REQUEST_TEMPLATE.md` for PR format
- `CONTRIBUTING.md` for project-specific guidelines
- `.github/workflows/` for CI checks that will run
- Project-specific linting configs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zmchenry) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
