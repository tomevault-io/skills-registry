---
name: finalize
description: > Use when this capability is needed.
metadata:
  author: acampb
---

# Finalize

Prepare code for commit by systematically checking for issues and running checks until everything passes.

## When to Use

Invoke `/finalize` when:

- Finishing a feature or bug fix
- User asks to "make it green", "prep for commit", or "production ready"
- Before handing code back to user for commit
- After a significant implementation session

**Note:** This skill prepares code for commit but does NOT create the commit itself.

## Before Starting

1. **Identify scope** - What files were changed in this session?

   ```bash
   git diff --name-only HEAD
   git diff --cached --name-only
   ```

2. **Understand context** - What was implemented? This informs what edge cases to check.

## Phase 1: Bug Check

Review changed files for common bugs and edge cases.

### Checklist

**Null/Undefined Handling:**

- [ ] Optional chaining used where data might be undefined
- [ ] Fallback values provided for potentially missing data
- [ ] Array methods handle empty arrays gracefully

**Edge Cases:**

- [ ] Empty states handled (empty arrays, null objects)
- [ ] Error states handled (network failures, invalid input)
- [ ] Loading states don't cause crashes
- [ ] Boundary conditions checked (0, negative, max values)

**Logic Errors:**

- [ ] Conditionals have correct operators (=== vs ==, && vs ||)
- [ ] Async/await used correctly (no missing await)
- [ ] Event handlers don't cause infinite loops
- [ ] State updates don't cause stale closures

**Type Safety:**

- [ ] No implicit `any` types
- [ ] Type assertions (`as`) are justified
- [ ] Validation at system boundaries (user input, external APIs)

## Phase 1b: Critical Path Review

> **Configure this section for your project's high-risk areas.**
>
> Common critical areas include:
> - **Authentication/Identity**: Token handling, session management, privilege escalation
> - **Data pipelines**: Idempotency, deduplication, ordering guarantees
> - **External integrations**: Rate limits, retry logic, credential handling
> - **Infrastructure provisioning**: State drift, rollback strategy, resource cleanup
>
> Add your project-specific critical areas to CLAUDE.md.

**If changes touch critical areas, do multiple passes:**

- **Pass 1**: Structural issues (transactions, error handling, idempotency)
- **Pass 2**: Logic gaps (edge cases, boundary conditions)
- **Pass 3**: Recovery scenarios (partial failure, retry, concurrent access)

### At Each Database Operation (if applicable)

- [ ] **Atomic?** - Use transactions for multi-step operations
- [ ] **Idempotent?** - Check for existing records before creating
- [ ] **What if it fails?** - Have recovery strategy
- [ ] **What if called twice?** - Prevent duplicate operations
- [ ] **What if concurrent?** - Handle race conditions

### Trace End-to-End Scenarios

- ✓ Normal flow (happy path)
- ✓ Network retry (API retry with same data)
- ✓ Partial failure (step 1 succeeds, step 2 fails)
- ✓ Race condition (two processes try same operation)
- ✓ Recovery (retry after previous failure)

## Phase 2: Consistency Check

Verify code follows project patterns.

### Checklist

**Naming:**

- [ ] Variables/functions use consistent casing (camelCase, snake_case, etc.)
- [ ] Components/classes use PascalCase
- [ ] Files match component/class names
- [ ] Boolean variables have clear prefixes (is/has/can/should)

**Imports:**

- [ ] Shared types imported from shared location
- [ ] UI components from shared UI package (if applicable)
- [ ] No circular imports

**Patterns:**

- [ ] Follows project's established patterns (check CLAUDE.md)
- [ ] Error handling is consistent with rest of codebase
- [ ] Tests follow project test patterns

**Structure:**

- [ ] Files in correct directories (by domain/feature)
- [ ] Exports added to index files where needed
- [ ] Related code colocated

## Phase 3: Production Readiness

Remove debug artifacts and ensure code is production-ready.

### Debug Code Removal

Search for and remove:

```bash
# Find console.logs in changed files
git diff --name-only HEAD | xargs grep -l "console.log" 2>/dev/null

# Find debugger statements
git diff --name-only HEAD | xargs grep -l "debugger" 2>/dev/null

# Find commented-out code blocks
git diff HEAD | grep "^+" | grep -E "//.*[a-zA-Z]+.*\(|/\*"
```

**Remove these:**

- `console.log()` - Remove unless it's intentional production logging
- `console.debug()` - Remove all
- `console.warn()` - Keep only if warning is useful in production
- `console.error()` - Keep if it's error reporting
- `debugger` statements - Remove all
- Commented-out code - Remove unless there's a clear reason
- `// TODO` from this session - Keep and warn user (see below)
- Test data / hardcoded values - Replace with real implementation

**Production Logging Guidelines:**

- `console.error()` - OK for actual errors that should be visible
- Structured logging (logger.info/warn/error) - OK if using logging framework
- Any other console.\* - Remove

### TODO Handling

Search for TODOs in changed files:

```bash
git diff --name-only HEAD | xargs grep -n "TODO\|FIXME\|XXX\|HACK" 2>/dev/null
```

**Action:**

- If TODO was created in this session: **Warn user** - present the list and ask if they should be addressed or left
- If TODO existed before: Leave as-is
- Never silently remove TODOs

### Other Production Checks

- [ ] No hardcoded localhost URLs (except in env examples)
- [ ] No hardcoded API keys or secrets
- [ ] No test-only code in production paths
- [ ] Error messages are user-friendly, not developer-speak
- [ ] No placeholder text ("Lorem ipsum", "TODO", "CHANGEME")

## Phase 4: Run Tests

Run the test suite to catch regressions:

```bash
# Use your project's test command
npm test
# or: yarn test, pnpm test, pytest, dotnet test, etc.
```

**If tests fail:**

1. Identify which tests failed
2. Determine if failure is due to:
   - Bug in new code → Fix it
   - Test needs updating for new behavior → Update test
   - Unrelated flaky test → Note and continue
3. Re-run tests after fixes

**Test Coverage:**

- New functionality should have tests
- If no tests exist for changed code, warn user

## Phase 5: Make It Green

Run the full check suite and fix issues iteratively.

```bash
# Use your project's check/lint command
npm run check
# or: npm run lint && npm run typecheck && npm run build
```

### Iteration Loop

```
1. Run check command
2. If passes → Done!
3. If fails:
   a. Identify the failing step (format/lint/typecheck/build)
   b. Fix the issues
   c. Go to step 1
```

### Common Fixes

**Format issues:**

- Usually auto-fixed by formatter
- If persisting, check for syntax errors

**Lint issues:**

- Read the error message carefully
- Common: unused imports, missing dependencies
- Fix: Remove unused code, add missing deps

**Type issues:**

- Type mismatches: Check expected vs actual types
- Missing properties: Add required fields or make optional
- Import errors: Check package exports

**Build issues:**

- Usually caused by type errors
- Check for circular dependencies
- Verify all imports resolve

### Max Iterations

If after 5 iterations issues persist:

1. Stop and report remaining issues
2. Ask user for guidance on unresolved problems

## Final Report

After all phases complete, present summary:

```markdown
## Finalize Summary

### Changes Reviewed

- {N} files checked

### Issues Found & Fixed

- Removed {N} console.log statements
- Fixed {N} lint errors
- Fixed {N} type errors

### Warnings

- {N} TODOs remain in code:
  - `path/to/file.ts:123` - TODO: description

### Status

✅ All checks passing - Ready for commit

### Files Changed

{list of files modified during finalize}
```

## Checklist

Before completing:

- [ ] Bug check completed on all changed files
- [ ] Critical path review if changes touch high-risk areas
- [ ] Consistency check completed
- [ ] Debug code removed (console.log, debugger)
- [ ] TODOs reviewed and user warned if any remain
- [ ] Tests pass
- [ ] Check command passes (green)
- [ ] Summary report presented to user
- [ ] No commit created (user will commit)

## Anti-Patterns

```typescript
// ❌ Leaving debug code
console.log('data:', data) // Remove!
debugger // Remove!

// ✓ Production-appropriate logging
logger.error('Failed to process request', { id, error })
```

```typescript
// ❌ Silently removing TODOs
// (just delete TODO comments without telling user)

// ✓ Warn about TODOs
// "Found 2 TODOs in changed files. Should I address these or leave them?"
```

```typescript
// ❌ Giving up after first failure
// "Check failed, here are the errors"

// ✓ Iterating until green
// Fix errors → re-run → repeat until passing
```

```typescript
// ❌ Creating a commit
git commit -m "..."  // Don't do this!

// ✓ Just prep the code
// "All checks passing. Ready for you to commit."
```

## Reference Files

- Project conventions: `CLAUDE.md`
- Check command: `package.json` (or project-specific config)
- Lint config: Project's lint configuration
- Test config: Project's test configuration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/acampb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
