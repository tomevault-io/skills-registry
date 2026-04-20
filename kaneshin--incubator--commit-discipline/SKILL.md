---
name: commit-discipline
description: This skill should be used when the user asks to "commit", "ready to commit", "save changes", "git commit", or when preparing to create a git commit. Ensures commits meet quality standards with all tests passing, no warnings, and clear messages. Use when this capability is needed.
metadata:
  author: kaneshin
---

# Commit Discipline Skill

## Purpose

Ensure every git commit meets quality standards before being created. Enforce that all tests pass, no compiler or linter warnings exist, commits represent single logical units of work, and commit messages clearly communicate intent.

## When to Use This Skill

Apply commit discipline when:
- About to create a git commit
- User requests `/commit` or similar command
- Ready to save work to version control
- Preparing to push changes
- Creating pull requests

## Pre-Commit Checklist

Before creating ANY commit, verify:

### 1. All Tests Passing

**Run full test suite:**
```bash
npm test        # JavaScript/TypeScript
pytest          # Python
cargo test      # Rust
go test ./...   # Go
```

**Requirements:**
- ✅ All tests must pass
- ✅ No skipped tests (unless documented as long-running)
- ✅ No flaky tests that randomly fail
- ❌ Never commit with failing tests

**If tests fail:**
1. Fix the failing tests
2. Re-run test suite
3. Only commit when all green

**Exception:** When working on a feature branch, you may commit work-in-progress with failing tests ONLY if:
- Clearly marked with "WIP:" prefix in commit message
- Not merged to main branch until tests pass
- Team convention allows WIP commits

### 2. No Compiler/Linter Warnings

**Check for warnings:**
```bash
npm run lint           # ESLint, Prettier
tsc --noEmit          # TypeScript compiler
cargo clippy          # Rust linter
pylint src/           # Python linter
```

**Requirements:**
- ✅ Zero warnings from compiler
- ✅ Zero warnings from linter
- ✅ Code follows project style guide
- ❌ Never commit with warnings

**If warnings exist:**
1. Fix all warnings
2. Re-run checks
3. Only commit when clean

**Allowed exceptions (rare):**
- Documented technical debt with tracking issue
- Third-party library warnings (add suppression with comment)
- Deprecated API usage with migration plan

### 3. Single Logical Unit of Work

Each commit should represent ONE coherent change.

**Good commit scope:**
- ✅ Add password validation feature
- ✅ Fix tax calculation bug
- ✅ Refactor: Extract pricing methods
- ✅ Update user profile component styling

**Bad commit scope:**
- ❌ Add feature X, fix bug Y, refactor Z (too many changes)
- ❌ Work in progress from today (vague)
- ❌ Various fixes (unclear)
- ❌ Update (meaningless)

**Atomic commits:**
- Each commit should be independently valuable
- Commit should be revertable without breaking system
- Changes should be related to single concern

**If commit is too large:**
1. Split into multiple logical commits
2. Each commit passes tests independently
3. Use `git add -p` for partial staging if needed

### 4. Clear Commit Message

**Commit message format:**

```
<Type>: <Subject> (50 characters or less)

<Optional body explaining what and why, not how>

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
```

**Type prefixes:**

**For structural changes:**
- `Refactor:` - Code restructuring without behavior change

**For behavioral changes:**
- `Add:` - New feature or capability
- `Fix:` - Bug fix
- `Update:` - Enhancement to existing feature
- `Remove:` - Delete feature or code

**For non-code changes:**
- `Docs:` - Documentation only
- `Test:` - Test additions or fixes
- `Chore:` - Build process, dependencies

**Subject guidelines:**
- Start with capital letter
- No period at end
- Imperative mood ("Add feature" not "Added feature")
- Be specific about what changed

**Good examples:**
```
Add discount calculation to checkout process
Fix tax calculation for international orders
Refactor: Extract validation methods from user service
Update button component with new brand colors
```

**Bad examples:**
```
updates             ❌ Vague
fixed stuff         ❌ Unclear
WIP                ❌ Not descriptive
asdf               ❌ Meaningless
```

**Optional body:**

For complex changes, add body explaining:
- **What** changed (if not obvious from subject)
- **Why** the change was needed
- Context or background
- Links to issues or design docs

**Example with body:**
```
Fix tax calculation for international orders

Previous implementation only handled US tax rates.
International orders were incorrectly taxed at US rates.

Now checks order country and applies appropriate tax rate.
Falls back to zero tax for unsupported countries.

Fixes #342
```

## Structural vs Behavioral Commits

Based on Tidy First methodology:

### Structural Commits (Refactoring)

**Always prefix with "Refactor:"**

```
Refactor: Extract pricing methods from order processor
Refactor: Rename confusing variable names in checkout
Refactor: Move validation logic to separate module
```

**Characteristics:**
- No behavior change
- All tests pass before and after
- Improves code structure, clarity, or maintainability
- Committed BEFORE behavioral changes

### Behavioral Commits (Features/Fixes)

**Use descriptive prefix:**

```
Add user authentication with JWT tokens
Fix memory leak in WebSocket connection handler
Update search to support fuzzy matching
```

**Characteristics:**
- Changes what code does
- New functionality or bug fixes
- Tests may be added to cover new behavior
- Committed AFTER structural changes (if both needed)

### Never Mix Both

❌ **Bad:**
```
Add discount feature and refactor pricing logic
```

✅ **Good:**
```
Commit 1: Refactor: Extract pricing methods
Commit 2: Add discount calculation feature
```

See Tidy First skill for detailed guidance on separating structural from behavioral changes.

## Git Workflow Integration

### Standard Commit Flow

**Working with `/commit` command:**

When using the `/commit` command:
1. Command runs git status and git diff automatically
2. Analyzes changes to draft commit message
3. Stages relevant files
4. Creates commit with proper message format
5. Includes Co-Authored-By line for Claude

**Pre-commit hooks:**
If project has pre-commit hooks (linting, formatting, tests):
- Hooks run automatically before commit
- If hooks fail, commit is blocked
- Fix issues and try commit again
- **NEVER** use `--no-verify` to skip hooks

### Commit Frequency

**Small, frequent commits preferred:**

✅ **Good:**
```
1. Add User model with validation
2. Add user repository with CRUD operations
3. Add user service layer
4. Add user API endpoints
5. Add user endpoint tests
```

❌ **Bad:**
```
1. Complete user feature (contains all above)
```

**Benefits of small commits:**
- Easier to review
- Easier to revert if needed
- Clearer history
- Faster feedback in CI/CD

**When to commit:**
- After each passing test in TDD cycle (Red → Green → commit)
- After each successful refactoring
- After completing a logical sub-task
- Before taking a break or switching tasks
- When all tests pass and code is clean

## Handling Different Scenarios

### Scenario 1: Tests Fail Before Commit

**Situation:** About to commit, run tests, tests fail.

**Response:**
1. Do NOT commit
2. Fix failing tests
3. Re-run tests until all pass
4. Then commit

**Never:**
- Commit with failing tests
- Use WIP commits on main branch
- Plan to "fix it later"

### Scenario 2: Linter Warnings Appear

**Situation:** Pre-commit hook fails due to linting errors.

**Response:**
1. Review linting errors
2. Fix all errors and warnings
3. Re-run linter
4. Commit when clean

**Never:**
- Use `--no-verify` to bypass hooks
- Ignore warnings
- Suppress without justification

### Scenario 3: Commit Is Too Large

**Situation:** Many files changed, multiple concerns addressed.

**Response:**
1. Use `git add -p` to stage changes interactively
2. Create first commit with related changes
3. Stage next group of related changes
4. Create second commit
5. Repeat until all changes committed

**Example:**
```bash
# Stage only refactoring changes
git add -p src/pricing.js
git commit -m "Refactor: Extract tax calculation methods"

# Stage feature changes
git add src/discount.js tests/discount.test.js
git commit -m "Add discount calculation feature"
```

### Scenario 4: Multiple Structural and Behavioral Changes

**Situation:** Refactored code AND added feature in same work session.

**Response:**
1. Stage refactoring changes first
2. Run tests (should pass)
3. Commit: "Refactor: ..."
4. Stage behavioral changes
5. Run tests (should pass)
6. Commit: "Add ..."

**Order:** Structural commits ALWAYS before behavioral commits.

## Integration with TDD Workflow

**TDD creates natural commit points:**

```
Red → Green → Refactor cycle:

1. Write failing test          (don't commit yet)
2. Make test pass              (commit: "Add feature X")
3. Refactor                    (commit: "Refactor: Extract Y")
4. Repeat
```

**Commit pattern:**
- After Green phase: Commit behavioral change
- After Refactor phase: Commit structural change (if significant refactoring)

**For small refactorings:**
- May include minor refactoring in same commit as feature
- Only if refactoring is trivial (renaming local variable)
- Prefer separate commits when in doubt

See TDD Workflow skill for detailed TDD guidance.

## Quality Gate Summary

Before committing, ensure:

- [ ] **Tests:** All tests passing, no skipped tests
- [ ] **Warnings:** No compiler or linter warnings
- [ ] **Scope:** Single logical unit of work
- [ ] **Message:** Clear, descriptive commit message
- [ ] **Type:** Properly labeled (Refactor: or descriptive prefix)
- [ ] **Separation:** Not mixing structural and behavioral changes

**If any item fails, fix it before committing.**

## Common Mistakes to Avoid

### Mistake 1: "I'll fix tests after committing"

❌ **Never commit with failing tests**, even temporarily.

### Mistake 2: Ignoring warnings

❌ **Warnings indicate problems.** Fix them, don't ignore them.

### Mistake 3: Generic commit messages

❌ "Update files", "Fix stuff", "Changes"

✅ "Fix tax calculation for zero-price items"

### Mistake 4: Massive commits

❌ One commit with 50 files changed across multiple features.

✅ Multiple small commits, each focused on one change.

### Mistake 5: Skipping pre-commit hooks

❌ `git commit --no-verify`

✅ Fix the issues that hooks are catching.

## Quick Reference

**Before committing:**
```bash
# 1. Run tests
npm test

# 2. Check linting
npm run lint

# 3. Review changes
git status
git diff

# 4. Stage changes
git add <files>

# 5. Commit with clear message
git commit -m "Type: Subject"
```

**Commit message template:**
```
Type: Subject (50 chars max)

Optional body explaining what and why.

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
```

**Types:**
- `Refactor:` - Structural changes
- `Add:` - New features
- `Fix:` - Bug fixes
- `Update:` - Enhancements
- `Docs:` - Documentation
- `Test:` - Tests

**Quality gates:**
- ✅ Tests pass
- ✅ No warnings
- ✅ Single concern
- ✅ Clear message
- ✅ Properly typed

## Additional Resources

**Reference files:**
- **`references/commit-message-examples.md`** - Extensive examples of good and bad commit messages
- **`references/git-workflow-patterns.md`** - Common git workflows and commit strategies

Consult these for detailed examples and advanced git techniques when needed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kaneshin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
