---
name: tdd-workflow
description: Complete Test-Driven Development (TDD) workflow guide. Use when implementing new features, adding functionality, or when user mentions "test first", "TDD", or "red-green-refactor". Ensures compliance with Critical Invariant #7 and provides step-by-step RED-GREEN-REFACTOR process. Use when this capability is needed.
metadata:
  author: arpa73
---

# Test-Driven Development (TDD) Workflow

This skill enforces Critical Invariant #7: Always write tests BEFORE implementation.

## When to Use This Skill

**ALWAYS use for:**
- New features or functionality
- New API endpoints or models
- New UI components with logic
- Bug fixes (write test that reproduces bug first)

**Trigger words:**
- "implement", "add feature", "create new"
- "TDD", "test first", "test-driven"
- "red-green-refactor"

## The TDD Cycle: RED-GREEN-REFACTOR

### 🔴 Phase 1: RED (Write Failing Test)

**Step 1: Create test file**
```bash
# If test file doesn't exist
touch test/my-feature.test.js
```

**Step 2: Write the test FIRST**
```javascript
import { describe, it } from 'node:test'
import assert from 'node:assert/strict'
import { myFeature } from '../lib/my-feature.js'

describe('myFeature', () => {
  it('should return expected result when given valid input', () => {
    const result = myFeature({ input: 'test' })
    assert.strictEqual(result.success, true)
    assert.strictEqual(result.output, 'processed: test')
  })
})
```

**Step 3: Run test - MUST FAIL**
```bash
npm test

# Expected output:
# ✗ myFeature > should return expected result...
#   Error: Cannot find module '../lib/my-feature.js'
```

**✅ Success criteria:**
- Test fails for the RIGHT reason
- Error message is clear
- You understand what needs to be implemented

**❌ If test passes already:**
- You're not writing a new test
- Test doesn't actually test the feature
- Implementation already exists (not TDD)

---

### 🟢 Phase 2: GREEN (Make Test Pass)

**Step 4: Implement MINIMAL code**

Write the SIMPLEST code that makes the test pass:

```javascript
// lib/my-feature.js

export function myFeature({ input }) {
  // Minimal implementation - just make the test pass
  return {
    success: true,
    output: `processed: ${input}`
  }
}
```

**Don't add:**
- Extra features not tested
- Edge case handling not in test
- Optimizations or clever code
- Multiple responsibilities

**Step 5: Run test - MUST PASS**
```bash
npm test

# Expected output:
# ✓ myFeature > should return expected result...
# Tests: 1 passed, 1 total
```

**✅ Success criteria:**
- All tests pass (including existing ones)
- Implementation is simple and clear
- Code does ONLY what tests require

---

### 🔵 Phase 3: REFACTOR (Improve While Green)

**Step 6: Make it better**

Now that tests pass, improve the code:

```javascript
// lib/my-feature.js

const PREFIX = 'processed: '

export function myFeature({ input }) {
  // Refactored: extracted constant, added validation
  if (!input || typeof input !== 'string') {
    throw new Error('Input must be a non-empty string')
  }
  
  return {
    success: true,
    output: `${PREFIX}${input.trim()}`
  }
}
```

**Step 7: Add tests for edge cases**
```javascript
describe('myFeature', () => {
  it('should return expected result when given valid input', () => {
    // ... existing test
  })

  it('should throw error for invalid input', () => {
    assert.throws(
      () => myFeature({ input: '' }),
      { message: 'Input must be a non-empty string' }
    )
  })

  it('should trim whitespace from input', () => {
    const result = myFeature({ input: '  test  ' })
    assert.strictEqual(result.output, 'processed: test')
  })
})
```

**Step 8: Run tests - MUST STILL PASS**
```bash
npm test

# Expected output:
# ✓ myFeature > should return expected result...
# ✓ myFeature > should throw error for invalid input
# ✓ myFeature > should trim whitespace from input
# Tests: 3 passed, 3 total
```

**✅ Success criteria:**
- Code is cleaner/more maintainable
- All tests still pass
- No new functionality without tests

---

## Full Workflow Example

### Scenario: Add a user validation function

**1. 🔴 RED: Write failing test**

```javascript
// test/validate-user.test.js
import { describe, it } from 'node:test'
import assert from 'node:assert/strict'
import { validateUser } from '../lib/validate-user.js'

describe('validateUser', () => {
  it('should return valid:true for user with required fields', () => {
    const user = { name: 'Alice', email: 'alice@example.com' }
    const result = validateUser(user)
    
    assert.strictEqual(result.valid, true)
    assert.strictEqual(result.errors, undefined)
  })
})
```

Run: `npm test` → ❌ Fails (module doesn't exist)

**2. 🟢 GREEN: Minimal implementation**

```javascript
// lib/validate-user.js
export function validateUser(user) {
  return { valid: true }
}
```

Run: `npm test` → ✅ Passes

**3. 🔵 REFACTOR: Add more tests + improve**

```javascript
// test/validate-user.test.js (add more tests)
describe('validateUser', () => {
  it('should return valid:true for user with required fields', () => {
    // ... existing test
  })

  it('should return valid:false when name is missing', () => {
    const user = { email: 'alice@example.com' }
    const result = validateUser(user)
    
    assert.strictEqual(result.valid, false)
    assert.deepStrictEqual(result.errors, ['Name is required'])
  })

  it('should return valid:false when email is invalid', () => {
    const user = { name: 'Alice', email: 'not-an-email' }
    const result = validateUser(user)
    
    assert.strictEqual(result.valid, false)
    assert.ok(result.errors.some(e => e.includes('email')))
  })
})
```

Run: `npm test` → ❌ Fails (need to implement validation)

```javascript
// lib/validate-user.js (improved implementation)
export function validateUser(user) {
  const errors = []
  
  if (!user.name || user.name.trim() === '') {
    errors.push('Name is required')
  }
  
  if (!user.email || !isValidEmail(user.email)) {
    errors.push('Valid email is required')
  }
  
  return {
    valid: errors.length === 0,
    errors: errors.length > 0 ? errors : undefined
  }
}

function isValidEmail(email) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)
}
```

Run: `npm test` → ✅ Passes

---

## Common Pitfalls

### ❌ Writing tests after implementation
**Problem:** Tests become "verification tests" not "design tests"
**Solution:** Delete implementation, start over with test first

### ❌ Writing too many tests at once
**Problem:** Overwhelmed, unclear what to implement
**Solution:** One test at a time, make it pass, then next test

### ❌ Implementing more than needed to pass test
**Problem:** Scope creep, untested code paths
**Solution:** Implement ONLY what makes current test pass

### ❌ Skipping RED phase
**Problem:** Test might always pass (false positive)
**Solution:** ALWAYS watch test fail before implementing

### ❌ Not refactoring
**Problem:** Code works but is messy
**Solution:** After GREEN, improve code while tests stay green

---

## TDD Benefits

### Design Benefits
- Forces thinking about API before implementation
- Creates minimal, focused interfaces
- Reveals coupling and dependencies early

### Confidence Benefits
- Every line of code has a test
- Refactoring is safe (tests catch regressions)
- Documentation through examples

### Workflow Benefits
- Clear progress (count passing tests)
- Natural stopping points
- Easy to resume work (look at failing test)

---

## Integration with Project Workflow

### Before Starting Feature
```bash
# 1. Read skill
cat .github/skills/tdd-workflow/SKILL.md

# 2. Create test file
touch test/my-feature.test.js

# 3. Start TDD cycle (RED)
```

### During Implementation
```bash
# Continuous cycle:
# 🔴 Write failing test
# 🟢 Make it pass
# 🔵 Refactor
# Repeat
```

### Before Committing
```bash
# Run all tests
npm test

# Git hook will check for test changes
git add lib/my-feature.js test/my-feature.test.js
git commit -m "feat: add my-feature (TDD)"
```

### Self-Audit (from AGENTS.md Step 3½)
- [ ] Did I write test BEFORE implementation? (RED)
- [ ] Did I see test fail first?
- [ ] Did I implement minimal code to pass? (GREEN)
- [ ] Did I refactor while keeping tests green? (REFACTOR)

---

## Enforcement Mechanisms

This project has 4 layers of TDD enforcement:

1. **AGENTS.md** - Pre-work checklist (Step 3)
2. **feature-implementation skill** - Phase 0 TDD requirement
3. **Git hook** - `.git-hooks/pre-commit` checks for test changes
4. **GitHub Actions** - `.github/workflows/tdd-compliance.yml` PR check

To install git hook:
```bash
./scripts/install-git-hooks.sh
```

---

## Quick Reference

**RED-GREEN-REFACTOR Cycle:**
1. 🔴 Write failing test
2. Run test (must fail)
3. 🟢 Write minimal code
4. Run test (must pass)
5. 🔵 Refactor code
6. Run test (must still pass)
7. Repeat

**Golden Rules:**
- Test FIRST, code second
- One test at a time
- Simplest code to pass
- All tests must pass before commit
- Refactor only when green

---

*This skill enforces Critical Invariant #7 from CODEBASE_ESSENTIALS.md*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arpa73) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
