---
name: pre-commit-checker
description: This skill activates when: Use when this capability is needed.
metadata:
  author: gregsuptown
---
---
name: pre-commit-checker
description: Run comprehensive quality checks before git commits. Validates TypeScript, tests, migrations, and catches common issues like console.log in production code or hardcoded secrets.
allowed-tools: Read, Grep, Glob, Bash
---

# Pre-Commit Quality Checker

## Purpose

Automated quality gate that runs before committing code to catch issues early:
- TypeScript compilation errors
- Database migration conflicts
- Console.log statements in production code
- TODOs without issue references
- Hardcoded secrets or API keys
- Missing tests for new service functions

## Auto-Invocation Triggers

This skill activates when:
- User asks to "commit changes" or "create a commit"
- User mentions "pre-commit" or "commit check"
- Before creating a pull request
- User runs git commit (via hook)

## Comprehensive Check List

### 1. TypeScript Compilation
```bash
npm run check
```
- ✅ All TypeScript files compile without errors
- ✅ No type errors in modified files
- ❌ Fail if compilation errors exist

### 2. Database Migration Safety
- ✅ Check for migration conflicts in `drizzle/meta/`
- ✅ Ensure sequential migration numbering
- ✅ No uncommitted schema changes
- ❌ Flag if schema.ts modified but no migration generated

### 3. Code Quality Checks

**Console.log Detection:**
```bash
grep -r "console\.log" server/ client/src/ --exclude-dir=node_modules
```
- ⚠️ Flag console.log in `server/` (production code)
- ⚠️ Flag console.log in `client/src/` except debug files
- ✅ Allow in test files and dev utilities

**TODO Comments:**
```bash
grep -rn "TODO" server/ client/src/ --exclude-dir=node_modules
```
- ✅ All TODOs have issue references: `TODO(#123): description`
- ❌ Fail if TODO without issue number
- Format: `TODO(#issue): what needs to be done`

### 4. Security Checks

**Hardcoded Secrets:**
Search for common patterns:
- `API_KEY = "`
- `SECRET = "`
- `PASSWORD = "`
- `TOKEN = "`
- `.env` files in git (should be ignored)

**Sensitive Files:**
- ❌ No `.env` files committed
- ❌ No `credentials.json` or similar
- ❌ No private keys (`.pem`, `.key`)

### 5. Test Coverage (New Services)
- Check if new files in `server/services/` have corresponding tests
- Flag service functions without test coverage
- Suggest test file creation

### 6. Import/Export Consistency
- ✅ No unused imports (TypeScript will catch this)
- ✅ No circular dependencies
- ✅ Consistent import paths (relative vs absolute)

## Check Process

### Step 1: Run TypeScript Check
```bash
npm run check
```

**Output:**
- ✅ Success: "TypeScript: No errors"
- ❌ Failure: List all compilation errors with file paths

### Step 2: Database Migration Check
1. Check if `server/db.ts` or `drizzle/schema.ts` modified
2. If yes, verify migration exists in `drizzle/meta/`
3. Check for migration number conflicts

**Output:**
- ✅ "Migrations: Clean" or "No schema changes"
- ⚠️  "Schema modified but no migration found"
- ❌ "Migration conflict detected"

### Step 3: Search for Issues
Run grep commands for:
- console.log statements
- TODO comments without issues
- Hardcoded secrets

**Output for each:**
```
⚠️  Found 3 console.log statements:
- server/routers.ts:45 (console.log in production code)
- client/src/pages/Debug.tsx:12 (OK - debug file)
- server/services/user.ts:89 (remove before commit)

Action required: 1 statement(s) in production code
```

### Step 4: Check for New Services
1. Find new files in `server/services/`
2. Check for corresponding test files
3. Flag if missing

**Output:**
```
⚠️  New service without tests:
- server/services/analytics.ts
  Suggested: Create server/services/analytics.test.ts
```

### Step 5: Security Scan
Search for sensitive patterns and files

**Output:**
- ✅ "No hardcoded secrets detected"
- ❌ "Found potential secret in server/config.ts:12"

## Output Format

### Success Case
```
✅ Pre-Commit Checks Passed

TypeScript: No errors
Migrations: Clean
Code Quality: No issues found
Security: No hardcoded secrets
Test Coverage: All services covered

Ready to commit!
```

### Warning Case
```
⚠️  Pre-Commit Checks - Issues Found

✅ TypeScript: No errors
✅ Migrations: Clean
⚠️  Code Quality: 2 issues
⚠️  Security: 1 potential issue
✅ Test Coverage: OK

Issues to review:

Code Quality:
- console.log in server/routers.ts:45
- TODO without issue in client/src/App.tsx:89

Security:
- Potential API key in server/config.ts:12
  Line: const API_KEY = "sk_live_..."

Recommendation: Fix issues before committing
```

### Failure Case
```
❌ Pre-Commit Checks Failed

❌ TypeScript: 5 errors found
✅ Migrations: Clean
⚠️  Code Quality: 1 issue
✅ Security: OK
✅ Test Coverage: OK

TypeScript Errors:
1. server/routers.ts:45:12 - Type 'string' is not assignable to type 'number'
2. client/src/App.tsx:23:5 - Property 'user' does not exist on type 'Context'
...

MUST FIX: TypeScript errors before committing
```

## Bypass Options

### When to Allow Bypass
- Work-in-progress commits on feature branches
- Emergency hotfixes (with approval)
- Refactoring commits (with team awareness)

### How to Bypass
Add flag to commit message:
```bash
git commit -m "WIP: Feature development [skip-checks]"
```

**Note:** Main branch should NEVER allow bypass

## Standards

### Critical (MUST FIX)
- ❌ TypeScript compilation errors
- ❌ Migration conflicts
- ❌ Hardcoded secrets in committed files
- ❌ .env files in git

### Warnings (SHOULD FIX)
- ⚠️  console.log in production code
- ⚠️  TODOs without issue references
- ⚠️  New services without tests
- ⚠️  Potential secrets (false positives allowed)

### Informational (NICE TO FIX)
- ℹ️  Code complexity warnings
- ℹ️  Import organization suggestions
- ℹ️  Documentation gaps

## Integration

### Git Hooks (Recommended)
Add to `.husky/pre-commit`:
```bash
#!/bin/sh
echo "Running pre-commit checks..."
npm run check || exit 1
# Add other checks here
```

### CI/CD Integration
Add to GitHub Actions:
```yaml
- name: Pre-commit checks
  run: |
    npm run check
    npm run lint
    npm run test
```

## Examples

### Example 1: Clean Commit
**Scenario:** Modified a React component, added proper types

**Checks:**
- ✅ TypeScript compiles
- ✅ No console.log
- ✅ No secrets
- ✅ Component has tests

**Output:** "✅ All checks passed. Ready to commit!"

### Example 2: Issues Found
**Scenario:** Added new service function, forgot to remove debug log

**Checks:**
- ✅ TypeScript compiles
- ⚠️  console.log in server/services/analytics.ts:45
- ⚠️  New service without tests
- ✅ No secrets

**Output:**
```
⚠️  Issues Found (2):
1. Remove console.log from server/services/analytics.ts:45
2. Add tests for server/services/analytics.ts

Fix these before committing? (recommended)
```

### Example 3: TypeScript Errors
**Scenario:** Changed function signature, missed updating callers

**Checks:**
- ❌ TypeScript: 3 errors in different files
- ✅ All other checks passed

**Output:**
```
❌ TypeScript Compilation Failed

Errors:
1. server/routers.ts:45 - Expected 2 arguments, got 3
2. client/src/pages/Admin.tsx:89 - Property 'id' is missing
3. server/services/user.ts:23 - Type mismatch

MUST FIX before committing
```

## Configuration

### Skip Files/Directories
Default skip list:
- `node_modules/`
- `dist/`, `build/`
- `.next/`, `out/`
- `coverage/`
- `*.test.ts`, `*.spec.ts` (for console.log check)
- `client/src/debug/` (for console.log check)

### Custom Patterns
Add to `.claude/settings.json`:
```json
{
  "skills": {
    "pre-commit-checker": {
      "skipConsoleLogs": ["client/src/debug/", "scripts/"],
      "requiredTodoFormat": "TODO\\(#\\d+\\)",
      "allowedSecretPatterns": ["PUBLIC_API_KEY"]
    }
  }
}
```

## Maintenance

### Weekly Review
- Are checks too strict? Too lenient?
- False positives to address?
- New patterns to detect?

### Monthly Update
- Update secret detection patterns
- Review bypass usage
- Adjust strictness based on team feedback

---

This skill acts as a safety net to catch common issues before they enter the codebase.
It's faster than CI/CD and more thorough than manual review.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gregsuptown) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
