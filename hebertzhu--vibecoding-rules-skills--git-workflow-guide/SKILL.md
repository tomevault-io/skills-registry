---
name: git-workflow-guide
description: Git workflow standards including commit message format, PR process, branch management, and feature implementation workflow with TDD and code review integration. Use when committing code, creating PRs, managing branches, or when asked about Git conventions, version control, commit standards, or pull request workflow. Use when this capability is needed.
metadata:
  author: hebertzhu
---

# Git Workflow Guide

Complete Git workflow standards for professional development with integrated TDD and code review.

## Quick Reference

### Commit Message Format

```
<type>: <description>

<optional body>
```

### Types

- **feat**: New feature
- **fix**: Bug fix
- **refactor**: Code refactoring (no functionality change)
- **docs**: Documentation updates
- **test**: Test-related changes
- **chore**: Build process or auxiliary tool changes
- **perf**: Performance optimization
- **ci**: CI/CD configuration changes

### Examples

```bash
# Good commit messages
feat: add user authentication feature
fix: resolve sidebar display issue
refactor: restructure API response handling logic
docs: update README installation instructions

# Bad commit messages
update code
fix bug
changes
```

### Rules

- Use present tense: "add feature" not "added feature"
- Lowercase first letter
- No period at the end
- Keep description concise (≤50 characters)
- Body is optional for detailed explanation

## Pull Request Workflow

### Pre-PR Checklist

1. **Analyze Complete Commit History**
   ```bash
   git log --oneline
   ```

2. **Review All Changes**
   ```bash
   git diff main...HEAD
   git diff --stat main...HEAD
   ```

3. **Ensure Tests Pass**
   ```bash
   npm run test
   npm run build
   npm run lint
   ```

### PR Creation Steps

1. **Push Branch** (use `-u` flag for new branches)
   ```bash
   git push -u origin feature/your-feature-name
   ```

2. **Write Detailed PR Summary**
   - Explain purpose and background of changes
   - List main change points
   - Include related issue links

3. **Include Test Plan**
   ```markdown
   ## Test Plan
   - [ ] Unit tests pass
   - [ ] Integration tests pass
   - [ ] Manual testing complete
   - [ ] Browser compatibility tested
   ```

4. **Add TODOs (if any)**
   ```markdown
   ## TODOs
   - [ ] Add more edge case tests
   - [ ] Update related documentation
   - [ ] Performance optimization
   ```

### PR Review Standards

Follow Code Quality Power's `code-review.md` for systematic review:
- Security check (CRITICAL)
- Logic correctness (HIGH)
- Error handling (HIGH)
- Test coverage (HIGH)
- Code quality (MEDIUM)

## Feature Implementation Workflow

### 1. Plan First

**Use Development Workflow Power's planner**

```
Steps:
1. Read planner.md steering file
2. Create implementation plan
3. Identify dependencies and risks
4. Break down into multiple phases
```

**Output**: Detailed implementation plan document

### 2. TDD Methodology

**Use Development Workflow Power's tdd-guide**

```
TDD Cycle:
1. Write test (RED) - Test should fail
2. Implement feature (GREEN) - Make test pass
3. Refactor (REFACTOR) - Improve code quality
4. Verify coverage (VERIFY) - Ensure 80%+ coverage
```

**Reference**: See testing standards in testing steering

### 3. Code Review

**Use Code Quality Power's code-reviewer**

```
Review Timing:
- Immediately after writing code
- Before submitting PR
- Before merging to main branch
```

**Priority Handling**:
- CRITICAL issues: Must fix
- HIGH issues: Should fix
- MEDIUM issues: Consider fixing
- LOW issues: Optional fix

### 4. Commit and Push

**Follow conventional commit format**

```bash
# 1. Stage changes
git add .

# 2. Commit (use standard format)
git commit -m "feat: add user authentication feature"

# 3. Push to remote
git push origin feature/user-auth
```

## Branch Management Strategy

### Branch Naming Convention

```
feature/feature-name    - New feature development
fix/issue-description   - Bug fix
refactor/refactor-content - Code refactoring
docs/documentation-update - Documentation changes
test/test-content       - Test-related
```

### Examples

```bash
feature/user-authentication
fix/sidebar-display-issue
refactor/api-response-handler
docs/update-readme
test/add-unit-tests
```

### Branch Lifecycle

1. **Create Branch**
   ```bash
   git checkout -b feature/new-feature
   ```

2. **Regularly Sync Main Branch**
   ```bash
   git fetch origin
   git rebase origin/main
   ```

3. **Delete After Merge**
   ```bash
   git branch -d feature/new-feature
   git push origin --delete feature/new-feature
   ```

## Code Review Checklist

Before submitting PR, self-review:

### Functionality
- [ ] Feature works as expected
- [ ] Edge cases handled
- [ ] Error handling implemented

### Code Quality
- [ ] Code is clear and readable
- [ ] Follows project coding standards
- [ ] No duplicate code
- [ ] Functions and variables clearly named

### Testing
- [ ] Unit tests added
- [ ] Test coverage ≥ 80%
- [ ] All tests pass
- [ ] Edge cases tested

### Documentation
- [ ] Code comments are clear
- [ ] README updated (if needed)
- [ ] API documentation updated (if needed)
- [ ] CHANGELOG updated

### Security
- [ ] No hardcoded keys or passwords
- [ ] Input validation implemented
- [ ] No SQL injection risk
- [ ] No XSS vulnerabilities

## Common Git Commands

### View Status and History

```bash
# View current status
git status

# View commit history
git log --oneline --graph --all

# View specific file history
git log --follow -- path/to/file

# View change statistics
git diff --stat
```

### Undo and Modify

```bash
# Undo working directory changes
git checkout -- file.txt

# Unstage file
git reset HEAD file.txt

# Modify last commit
git commit --amend

# Undo last commit (keep changes)
git reset --soft HEAD~1
```

### Branch Operations

```bash
# View all branches
git branch -a

# Switch branch
git checkout branch-name

# Create and switch to new branch
git checkout -b new-branch

# Delete local branch
git branch -d branch-name

# Delete remote branch
git push origin --delete branch-name
```

## Common Issues and Solutions

### Issue 1: Wrong Commit Message

```bash
# Modify last commit message
git commit --amend -m "correct commit message"

# If already pushed, need force push (use cautiously)
git push --force-with-lease
```

### Issue 2: Forgot to Switch Branch

```bash
# Stash current work
git stash

# Switch to correct branch
git checkout correct-branch

# Restore work
git stash pop
```

### Issue 3: Need to Combine Multiple Commits

```bash
# Interactive rebase (combine last 3 commits)
git rebase -i HEAD~3

# In editor, mark commits to squash
# Save and exit, then edit combined commit message
```

### Issue 4: Conflict Resolution

```bash
# 1. Pull latest code
git fetch origin
git rebase origin/main

# 2. Resolve conflicts (edit conflict files)

# 3. Mark as resolved
git add .

# 4. Continue rebase
git rebase --continue

# If need to abort rebase
git rebase --abort
```

## Integration with Development Workflow

### Align Phase
- Create feature branch
- Document requirements in ALIGNMENT.md

### Architect Phase
- Design documented in DESIGN.md
- Commit design decisions

### Atomize Phase
- Break into tasks in TASK.md
- Each task = separate commit

### Automate Phase
- Follow TDD for each task
- Commit after each task completion
- Use conventional commit format

### Assess Phase
- Create PR with complete summary
- Request code review
- Address review feedback
- Merge after approval

## Best Practices Summary

1. **Frequent Commits** - Small steps, each commit does one thing
2. **Clear Messages** - Let others (and future you) quickly understand changes
3. **Timely Sync** - Regularly pull from main branch, avoid large conflicts
4. **Code Review** - Self-review before submitting, request others' review after
5. **Test First** - Follow TDD, ensure code quality
6. **Sync Documentation** - Update documentation when code changes

## Related Documentation

- **Testing Standards**: See testing steering
- **Code Review**: See code-quality power's code-review.md
- **Development Workflow**: See development-workflow power
- **Coding Standards**: See coding steering

---

*Remember: Good Git workflow is the foundation of team collaboration. Clear commits and systematic reviews make everyone's life easier.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hebertzhu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
