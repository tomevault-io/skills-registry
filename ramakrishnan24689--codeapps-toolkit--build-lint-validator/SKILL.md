---
name: build-lint-validator
description: Validate TypeScript compilation and ESLint compliance before code delivery. Use after implementation to ensure production-ready code quality. Use when this capability is needed.
metadata:
  author: ramakrishnan24689
---

# Build & Lint Validator Skill

## Purpose

Systematically validate that implemented code compiles successfully and passes ESLint rules before delivery. This skill acts as the final quality gate to catch compilation errors, type issues, and linting violations that would otherwise be discovered by the user during their own build process.

## When to Use This Skill

**ALWAYS use after**:
- Completing feature implementation (Phase 3 of implementation workflow)
- Making significant code changes across multiple files
- Before creating pull requests or commits
- Before marking implementation tasks as complete

**Use to validate**:
- TypeScript compilation success (no type errors)
- ESLint rule compliance (no linting violations)
- Build process completeness (all dependencies resolved)
- Code quality standards adherence

## Validation Framework

This skill performs **3 critical checks**:

### 1. TypeScript Compilation Check
Ensures all TypeScript code compiles without errors.

### 2. ESLint Compliance Check
Verifies code passes all ESLint rules and style guidelines.

### 3. Auto-Fix Capability
Attempts to automatically fix common linting issues when possible.

## Validation Process

### Step 1: Run TypeScript Compilation

Execute the build command to check for compilation errors:

```bash
npm run build
```

**What to check**:
- [ ] Build exits with code 0 (success)
- [ ] No TypeScript compilation errors
- [ ] No missing dependencies
- [ ] All imports resolve correctly
- [ ] All type definitions valid

**Common TypeScript errors**:
- Type mismatches (e.g., `Type 'string' is not assignable to type 'number'`)
- Missing imports (e.g., `Cannot find module '@/components/Button'`)
- Undefined properties (e.g., `Property 'foo' does not exist on type 'Bar'`)
- Generic type errors (e.g., `Type 'X' does not satisfy the constraint 'Y'`)

### Step 2: Run ESLint Check

Execute the lint command to check for style violations:

```bash
npm run lint
```

**What to check**:
- [ ] Lint exits with code 0 (success)
- [ ] No ESLint errors
- [ ] No ESLint warnings (if strict mode)
- [ ] All files pass configured rules
- [ ] No unused imports or variables

**Common ESLint errors**:
- Unused variables (e.g., `'foo' is defined but never used`)
- Missing dependencies in useEffect (e.g., `React Hook useEffect has a missing dependency`)
- Incorrect hook usage (e.g., `React Hook "useState" is called conditionally`)
- Console statements (e.g., `Unexpected console statement`)
- Import order violations

### Step 3: Attempt Auto-Fix (If Issues Found)

If ESLint reports fixable issues, attempt automatic fixes:

```bash
npm run lint -- --fix
```

**Auto-fixable issues include**:
- Import sorting and organization
- Semicolon insertion/removal
- Whitespace and formatting
- Unused imports removal
- Quote style consistency

**Non-fixable issues require manual intervention**:
- Logic errors
- Type errors
- Complex rule violations
- Architectural issues

### Step 4: Generate Validation Report

Provide structured feedback on validation results.

## Validation Report Format

```markdown
# Build & Lint Validation Report
**Date**: [current date]
**Status**: ✅ PASS | ⚠️ WARNINGS | ❌ FAIL

## Summary
- **TypeScript Compilation**: ✅ PASS | ❌ FAIL (X errors)
- **ESLint Check**: ✅ PASS | ❌ FAIL (X errors, Y warnings)
- **Auto-Fix Applied**: Yes | No | N/A

## TypeScript Compilation Results

### Errors (X found)
1. [File path:line:column]: [Error message]
   - **Type**: Type error | Import error | Syntax error
   - **Fix**: [Suggested fix]

2. [File path:line:column]: [Error message]
   - **Type**: Type error | Import error | Syntax error
   - **Fix**: [Suggested fix]

### Build Output
```
[Relevant build output]
```

## ESLint Results

### Errors (X found)
1. [File path:line:column]: [Rule name] - [Error message]
   - **Auto-fixable**: Yes | No
   - **Fix**: [Suggested fix or "Auto-fixed"]

2. [File path:line:column]: [Rule name] - [Error message]
   - **Auto-fixable**: Yes | No
   - **Fix**: [Suggested fix or "Auto-fixed"]

### Warnings (Y found)
1. [File path:line:column]: [Rule name] - [Warning message]
   - **Recommendation**: [Suggested action]

### Lint Output
```
[Relevant lint output]
```

## Recommendations

### Immediate Actions (Blockers)
1. [Action to fix critical issues]
2. [Action to fix critical issues]

### Suggested Improvements
1. [Recommended code quality improvements]
2. [Recommended code quality improvements]

## Next Steps
- [ ] Fix all TypeScript compilation errors
- [ ] Fix all ESLint errors
- [ ] Address ESLint warnings (if applicable)
- [ ] Re-run validation
- [ ] Proceed to delivery/commit phase
```

## Usage Pattern

**Typical workflow**:
```
1. Developer/Agent: Implements feature code
2. Developer/Agent: Invokes build-lint-validator skill
3. Validator: Runs TypeScript compilation check
4. Validator: Runs ESLint check
5. If issues found: Attempts auto-fix with eslint --fix
6. Validator: Generates validation report
7. If failed: Developer/Agent fixes issues and re-validates
8. If passed: Proceed to commit/PR creation
```

**Manual invocation**:
```
User: "Validate the build and lint status"
System: Runs build-lint-validator skill
```

**Proactive invocation** (recommended):
```
Agent: [Completes feature implementation]
Agent: [Automatically invokes build-lint-validator]
Agent: [Reports validation results to user]
Agent: [Fixes issues if found, or marks task complete if passed]
```

## Integration with Workflow

This skill integrates at two key points:

### Phase 3: Post-Implementation Validation
- **Trigger**: After completing code generation for a feature
- **Purpose**: Catch issues immediately after implementation
- **Action**: Run full build + lint check
- **Outcome**: Either proceed to Phase 4 (testing) or return to Phase 3 (fix issues)

### Phase 5: Pre-Delivery Validation
- **Trigger**: Before creating commits or pull requests
- **Purpose**: Final quality gate before delivery
- **Action**: Re-run build + lint check to ensure no regressions
- **Outcome**: Either proceed to commit/PR or fix any issues introduced

## Error Handling

### Build Failures

**Type errors**:
```typescript
// Error: Type 'string | undefined' is not assignable to type 'string'
const name: string = user?.name;

// Fix: Add type guard or default value
const name: string = user?.name ?? 'Unknown';
```

**Import errors**:
```typescript
// Error: Cannot find module '@/components/Button'
import { Button } from '@/components/Button';

// Fix: Verify path and component export
import { Button } from '@/components/Button/Button';
// OR
import Button from '@/components/Button';
```

### Lint Failures

**Unused variables**:
```typescript
// Error: 'data' is assigned a value but never used
const [data, setData] = useState();

// Fix: Remove if truly unused, or prefix with underscore if intentionally unused
const [_data, setData] = useState();
```

**React Hook dependency warnings**:
```typescript
// Warning: React Hook useEffect has a missing dependency: 'fetchData'
useEffect(() => {
  fetchData();
}, []);

// Fix: Add dependency or wrap in useCallback
useEffect(() => {
  fetchData();
}, [fetchData]);
```

## Common Fix Patterns

See [references/common-fixes.md](./references/common-fixes.md) for detailed fix patterns for frequent errors.

## Success Criteria

**Validation passes when**:
- ✅ `npm run build` exits with code 0
- ✅ `npm run lint` exits with code 0
- ✅ No TypeScript compilation errors
- ✅ No ESLint errors
- ✅ All warnings addressed or justified

**Validation fails when**:
- ❌ Build command fails
- ❌ TypeScript compilation errors present
- ❌ ESLint errors present
- ❌ Critical warnings unaddressed

## Performance Considerations

- **Build time**: Typically 10-60 seconds depending on project size
- **Lint time**: Typically 5-30 seconds depending on file count
- **Auto-fix time**: Adds 5-10 seconds if fixable issues found
- **Total validation time**: Expect 20-100 seconds for full check

**Optimization tips**:
- Run lint check first (faster failure detection)
- Only run full build if lint passes
- Cache build results when possible
- Use incremental TypeScript compilation

## Exit Codes

This skill uses standard exit codes:
- **0**: All checks passed
- **1**: TypeScript compilation failed
- **2**: ESLint check failed
- **3**: Both build and lint failed

---

**Usage**: Invoke this skill after implementing features to ensure production-ready code quality. Automatically invoked by implementation agents during Phase 3 (post-implementation) and Phase 5 (pre-delivery) validation gates.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ramakrishnan24689) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
