---
name: test-doctor
description: > Use when this capability is needed.
metadata:
  author: vperreard
---

# Test Doctor - Diagnostic & Repair Methodology

**Mission**: Repair broken tests surgically, one at a time, with validation at each step.

**Core Principle**: 🩺 **Diagnostic BEFORE Treatment** (never blind fixes)

**Usage**: Can be invoked by User OR by specialized agents for guided workflow

---

## 🚨 Critical Rules (MUST Follow)

1. ❌ **NEVER** fix multiple tests in one go
2. ❌ **NEVER** fix without understanding root cause
3. ❌ **NEVER** skip validation step
4. ✅ **ALWAYS** consult DONT_DO.md first (anti-patterns)
5. ✅ **ALWAYS** run test in isolation before fixing
6. ✅ **ALWAYS** validate fix doesn't break other tests

---

## 📋 Diagnostic Workflow (Mandatory Steps)

### Step 1: ISOLATE & REPRODUCE

```bash
# Run single test file in isolation
npm test -- path/to/failing-test.test.ts

# If still fails, run single test case
npm test -- path/to/failing-test.test.ts -t "specific test name"
```

**Decision Point**:
- ✅ Passes in isolation? → **Problem: Test interdependency or setup order**
- ❌ Fails in isolation? → Continue to Step 2

---

### Step 2: CLASSIFY ERROR TYPE

Analyze error message and classify:

**A) TypeScript Compilation Error**
```
Type 'X' is not assignable to type 'Y'
Property 'foo' does not exist on type 'Bar'
```
→ **Root Cause**: Code source changed, test outdated
→ **Fix**: Update test types OR fix code types
→ **Consult**: `Skill("prisma-relations-mapping")` if Prisma/API related

**B) Import/Dependency Error**
```
Cannot find module '../path/to/file'
ReferenceError: X is not defined
```
→ **Root Cause**: File moved, renamed, or missing
→ **Fix**: Update import paths OR restore missing file
→ **Consult**: `Skill("architecture-lookup")` for current structure

**C) Assertion/Logic Error**
```
Expected: X, Received: Y
AssertionError: expect(received).toBe(expected)
```
→ **Root Cause**: Code behavior changed OR test expectation wrong
→ **Fix**: Verify code logic FIRST, then update test if code is correct
→ **Consult**: DONT_DO.md for known anti-patterns

**D) Async/Timeout Error**
```
Timeout - Async callback was not invoked within the 5000 ms timeout
```
→ **Root Cause**: Missing await, improper mock, or cleanup issue
→ **Fix**: Add proper async/await, fix mocks, add cleanup
→ **Pattern**: See `resources/async-patterns.md`

**E) Mock/Setup Error**
```
Cannot read property 'X' of undefined
Mock function not called
```
→ **Root Cause**: Incorrect mock setup or missing beforeEach/afterEach
→ **Fix**: Verify mock configuration, add proper setup/teardown
→ **Pattern**: See `resources/mock-patterns.md`

**F) Invalid Test Data / API Validation Error** ⭐ **MATHILDANESTH-SPECIFIC**
```
Expected: 201 (Created)
Received: 422 (Validation error)
Error: "Données de validation invalides"
```
→ **Root Cause**: Test data missing required fields (Prisma migrations, schema changes)
→ **Fix**: Add required fields from Prisma schema (consult Skill("prisma-relations-mapping"))
→ **Pattern**: See `resources/mathildanesth-specific-patterns.md` (Pattern MS-1, MS-6)
→ **Frequency**: TRÈS HAUTE (~35-45% des tests cassés)

---

### Step 3: CONSULT DOCUMENTATION (Before Fixing!)

**Mandatory checks in order**:

1. **`Skill("anti-patterns")`** (DONT_DO.md)
   - Check if error is a known anti-pattern
   - Example: "Never mock Prisma client directly in integration tests"

2. **Context-specific skills**:
   - API/Prisma error? → `Skill("prisma-relations-mapping")`
   - UI component error? → `Skill("frontend-patterns")`
   - Schema/model error? → `Skill("architecture-lookup")`

3. **BREAKING_CHANGES.md** (if error appeared after git pull)
   - Check recent migrations (e.g., PascalCase migration Oct 26)
   - Check removed models (e.g., OnCall removal Oct 24)

4. **⭐ Mathildanesth-specific patterns** (CONSULT FIRST for high-frequency errors):
   - `resources/mathildanesth-specific-patterns.md` - 6 patterns couvrant 75-90% des tests cassés
   - Pattern MS-1: API Validation 422 (~30-40% des erreurs)
   - Pattern MS-6: Prisma Required Fields (~35-45% des erreurs)
   - Pattern MS-4: Global Mocks Removed (~20% des erreurs)
   - Pattern MS-5: Auth Mock Missing (~25% des erreurs)

5. **Test-specific resources** (generic patterns):
   - `resources/error-patterns.md` - Catalog of recurring errors
   - `resources/fix-strategies.md` - Proven fix strategies

---

### Step 4: FIX MINIMALLY (One Change Only)

**Decision Tree**:

```
Error classified?
  ↓
Is CODE source wrong?
  YES → Fix code source
  NO  → Continue
  ↓
Is TEST outdated/wrong?
  YES → Update test expectations
  NO  → Continue
  ↓
Is SETUP/MOCK wrong?
  YES → Fix setup/mock configuration
  NO  → Continue
  ↓
Is FILE STRUCTURE wrong?
  YES → Update imports/paths
  NO  → Escalate (complex issue)
```

**Apply ONE fix only**:
- If code is wrong → Fix code (not test)
- If test is wrong → Fix test (not code)
- If both unclear → **ASK USER** with AskUserQuestion tool

---

### Step 5: VALIDATE (Mandatory)

```bash
# 1. Run fixed test in isolation
npm test -- path/to/test.test.ts

# 2. If passes, run ALL tests in same file
npm test -- path/to/test.test.ts

# 3. If still passes, run related tests
npm test -- path/to/related-module/

# 4. Only if ALL pass, consider success
```

**Validation Checklist**:
- ✅ Test passes in isolation?
- ✅ All tests in same file pass?
- ✅ No new TypeScript errors? (`npm run typecheck:backend` or `typecheck:frontend`)
- ✅ No new lint errors? (`npm run lint`)

**If ANY validation fails**:
1. Revert the fix immediately
2. Return to Step 2 (reclassify error)
3. Consider alternative root cause

---

## 🎯 Common Patterns & Quick Fixes

### Pattern 1: Prisma PascalCase Migration (Oct 26, 2025)

**Error**:
```
Property 'user' does not exist on type 'PrismaClient'
```

**Fix**:
```typescript
// ❌ Old (snake_case)
await prisma.user.findMany()

// ✅ New (PascalCase)
await prisma.user.findMany() // Already correct! Check import
```

**Consult**: `Skill("prisma-relations-mapping")` for correct model names

---

### Pattern 2: React Testing Library Async Updates

**Error**:
```
Warning: An update to Component inside a test was not wrapped in act(...)
```

**Fix**:
```typescript
// ❌ Wrong
render(<Component />)
expect(screen.getByText('Loading')).toBeInTheDocument()

// ✅ Correct
render(<Component />)
await waitFor(() => {
  expect(screen.getByText('Loaded')).toBeInTheDocument()
})
```

**Consult**: `resources/async-patterns.md`

---

### Pattern 3: Mock Cleanup

**Error**:
```
Test suite failed to run
jest.mock is already defined
```

**Fix**:
```typescript
// Add cleanup in afterEach
afterEach(() => {
  jest.clearAllMocks()
  jest.resetModules()
})
```

**Consult**: `resources/mock-patterns.md`

---

## 📊 Success Metrics (Track Progress)

After each fix, report:
- ✅ Test file: `path/to/test.test.ts`
- ✅ Error type: (A/B/C/D/E from Step 2)
- ✅ Root cause: Brief explanation
- ✅ Fix applied: One-line summary
- ✅ Validation: All checks passed? (Yes/No)
- ✅ Tests now passing: X/Y in file

**Example Report**:
```
✅ Fixed: src/app/api/leaves/__tests__/route.test.ts
   Error Type: C (Assertion Error)
   Root Cause: API response schema changed (PascalCase migration)
   Fix: Updated test expectations to match new schema
   Validation: ✅ All checks passed
   Tests: 12/12 passing in file
```

---

## 🔄 Escalation Protocol

**When to escalate** (ask user):
1. Root cause unclear after Step 2 classification
2. Fix requires architectural decision (e.g., change API contract)
3. Fix breaks other tests (validation fails)
4. Error pattern not documented in resources

**Use AskUserQuestion tool** with:
- Clear description of the issue
- Your diagnostic findings
- 2-3 options with trade-offs
- Your recommended approach

---

## 📚 Progressive Resources

For detailed patterns and strategies:
- **`resources/error-patterns.md`** - Catalog of 20+ recurring error patterns
- **`resources/fix-strategies.md`** - Proven fix strategies by error type
- **`resources/async-patterns.md`** - Async/await patterns for RTL
- **`resources/mock-patterns.md`** - Mock setup patterns (Prisma, API, WebSocket)
- **`resources/validation-checklist.md`** - Complete validation checklist

---

## 🎓 Learning Loop

After each successful fix:
1. Document pattern if new → Add to `resources/error-patterns.md`
2. Update DONT_DO.md if anti-pattern discovered
3. Share fix strategy with team (commit message)

---

**Version**: 1.0.0 (November 2025)
**Maintainer**: Test Doctor Skill
**Last Update**: Based on 1211 test files, 540 failing tests context

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vperreard) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
