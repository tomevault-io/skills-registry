---
name: tdd-workflow
description: This skill should be used when the user asks to "implement a feature", "add functionality", "write a test", "use TDD", "test-driven development", "write failing test first", or when beginning any new feature development. Guides through the Red → Green → Refactor cycle with strict test-first discipline. Use when this capability is needed.
metadata:
  author: kaneshin
---

# TDD Workflow Skill

## Purpose

Guide development through Test-Driven Development (TDD) methodology using the Red → Green → Refactor cycle. Ensure tests drive implementation, code remains minimal and focused, and quality improves through continuous refactoring.

## When to Use This Skill

Apply TDD workflow when:
- Implementing any new feature or functionality
- Fixing a defect (write test that exposes the bug first)
- Adding capabilities to existing code
- Building new components or modules

Do not skip TDD even for "simple" features—the discipline ensures quality and prevents defects.

## The Red → Green → Refactor Cycle

### Phase 1: Red (Write a Failing Test)

Start by writing the smallest possible test that defines one increment of desired functionality.

**Critical principles:**
- Write test BEFORE any implementation code
- Test should fail initially (verify it fails!)
- Focus on one specific behavior
- Use descriptive test names that explain the behavior

**Test naming convention:**
```
shouldReturnTrueWhenInputIsValid
shouldThrowErrorWhenArgumentIsNull
shouldCalculateTotalForMultipleItems
shouldRenderButtonWithCorrectLabel
```

Format: `should[ExpectedBehavior]When[Condition]`

**Example workflow:**
1. Identify the next small increment of functionality needed
2. Write a test that would pass if that functionality existed
3. Run the test—verify it fails with a clear error message
4. Ensure the failure message is informative (helps debug later)

**Common mistake to avoid:** Writing multiple tests before implementation. Write ONE test, make it pass, then write the next test.

### Phase 2: Green (Make It Pass with Minimal Code)

Implement only the code necessary to make the current failing test pass—nothing more.

**Critical principles:**
- Write the simplest code that could possibly work
- Resist the urge to add extra features or abstraction
- Hard-code values if that makes the test pass (refactor later)
- No premature optimization or "future-proofing"

**Minimal implementation strategy:**
1. Read the failing test
2. Identify the exact behavior it expects
3. Write code that produces that behavior
4. Run tests—verify the failing test now passes
5. Run ALL tests—ensure no regressions

**Example progression:**

*Test 1:*
```javascript
test('shouldReturnZeroWhenArrayIsEmpty', () => {
  expect(sum([])).toBe(0);
});
```

*Minimal implementation:*
```javascript
function sum(numbers) {
  return 0; // Hard-coded to pass test
}
```

*Test 2:*
```javascript
test('shouldReturnNumberWhenArrayHasOneElement', () => {
  expect(sum([5])).toBe(5);
});
```

*Minimal implementation:*
```javascript
function sum(numbers) {
  if (numbers.length === 0) return 0;
  return numbers[0]; // Still simple
}
```

*Test 3:*
```javascript
test('shouldReturnSumWhenArrayHasMultipleElements', () => {
  expect(sum([1, 2, 3])).toBe(6);
});
```

*Now implement the real algorithm:*
```javascript
function sum(numbers) {
  return numbers.reduce((acc, n) => acc + n, 0);
}
```

**Run all tests after EVERY implementation change.** Verify:
- The current test passes
- All previous tests still pass
- No compiler/linter warnings introduced

### Phase 3: Refactor (Improve Structure While Tests Pass)

With passing tests protecting behavior, improve code structure, clarity, and maintainability.

**Critical principles:**
- Only refactor when ALL tests are passing (green)
- Run tests after EACH refactoring change
- Focus on one refactoring at a time
- If tests fail during refactoring, revert immediately

**Refactoring opportunities to look for:**
- Duplication between tests or implementation
- Unclear variable or function names
- Long methods that should be extracted
- Complex conditionals that could be simplified
- Magic numbers that should be named constants

**Refactoring workflow:**
1. Identify improvement opportunity
2. Make one small refactoring change
3. Run all tests—verify they still pass
4. Commit if tests pass, revert if they fail
5. Repeat until code quality meets standards

**Example refactorings:**

*Extract method:*
```javascript
// Before
function processOrder(order) {
  const tax = order.total * 0.08;
  const shipping = order.total > 50 ? 0 : 5.99;
  return order.total + tax + shipping;
}

// After
function processOrder(order) {
  return order.total + calculateTax(order) + calculateShipping(order);
}

function calculateTax(order) {
  return order.total * 0.08;
}

function calculateShipping(order) {
  return order.total > 50 ? 0 : 5.99;
}
```

*Rename for clarity:*
```javascript
// Before
function calc(n) { return n * 1.08; }

// After
function calculateTotalWithTax(subtotal) { return subtotal * 1.08; }
```

**Never skip running tests after refactoring.** Automated tests are the safety net that makes refactoring safe.

## Complete TDD Cycle Example

Implementing a `Password` validator:

**Iteration 1:**
```
Red:   test('shouldRejectPasswordShorterThanEightCharacters')
Green: return password.length >= 8;
```

**Iteration 2:**
```
Red:   test('shouldRejectPasswordWithoutUppercase')
Green: return password.length >= 8 && /[A-Z]/.test(password);
```

**Iteration 3:**
```
Red:   test('shouldRejectPasswordWithoutNumber')
Green: return password.length >= 8 && /[A-Z]/.test(password) && /[0-9]/.test(password);
```

**Refactor:**
```javascript
function validatePassword(password) {
  return hasMinimumLength(password)
    && containsUppercase(password)
    && containsNumber(password);
}

function hasMinimumLength(password) {
  return password.length >= 8;
}

function containsUppercase(password) {
  return /[A-Z]/.test(password);
}

function containsNumber(password) {
  return /[0-9]/.test(password);
}
```

Run tests after refactoring—all should pass.

## Handling Defects with TDD

When fixing a bug, follow this enhanced workflow:

1. **Write API-level failing test**: Test at the highest level that exposes the defect
2. **Write unit-level failing test**: Test at the smallest level that replicates the problem
3. **Implement the fix**: Make both tests pass with minimal changes
4. **Run all tests**: Ensure no regressions introduced
5. **Refactor if needed**: Clean up the fix while tests protect behavior

**Example:** Bug in checkout calculation

*API-level test:*
```javascript
test('shouldCalculateCorrectTotalForOrderWithDiscount', () => {
  const order = { items: [10, 20], discount: 5 };
  expect(processOrder(order)).toBe(25);
});
```

*Unit-level test:*
```javascript
test('shouldSubtractDiscountFromSubtotal', () => {
  expect(applyDiscount(30, 5)).toBe(25);
});
```

Both tests should fail initially, confirming they expose the defect. Fix the code to make both pass.

## Running Tests

**After every code change, run all tests.** No exceptions.

Framework-agnostic test execution:
- Identify the test command for the project (npm test, pytest, cargo test, etc.)
- Run the full test suite to catch regressions
- Fix any failures before proceeding
- Only skip long-running integration tests during rapid iteration (run them before committing)

**Test output interpretation:**
- ✅ All passing: Safe to proceed to next step
- ❌ Any failures: Stop and fix before continuing
- ⚠️ Warnings: Address before committing

## Critical Rules

**Never write implementation code without a failing test:**
- If tempted to add functionality, write the test first
- If "just trying something," write a test to verify it works
- No exceptions, even for "obvious" code

**One test at a time:**
- Write one failing test
- Make it pass
- Refactor
- Repeat
- Don't write multiple tests ahead of implementation

**Run all tests every time:**
- After writing a test (should fail)
- After implementation (should pass)
- After each refactoring step (should still pass)
- Before committing (all must pass)

**Minimal implementation only:**
- Resist adding extra features
- Avoid "while I'm here" additions
- Don't optimize prematurely
- Simplest code that passes tests

**Refactor only when green:**
- Never refactor with failing tests
- Run tests after each refactoring change
- Revert immediately if tests fail during refactoring

## Integration with Other Workflows

**With Tidy First approach:** Structural refactorings during the Refactor phase should be committed separately from behavioral changes (the Red → Green implementation). See the Tidy First skill for guidance on separating commits.

**With commit discipline:** Only commit when ALL tests pass and no warnings exist. Each Red → Green → Refactor cycle may produce one or more commits. See the Commit Discipline skill for commit standards.

**With UI development:** Apply TDD to UI components by writing tests for component behavior first (rendering, interactions, state changes) before implementation. See the UI Development skill for frontend-specific guidance.

## Common Pitfalls and Solutions

**Pitfall 1: Writing too much test code before implementation**
- Solution: Write ONE simple test, make it pass, repeat

**Pitfall 2: Implementing more than needed to pass the test**
- Solution: Ask "What's the simplest code that makes this test pass?" Hard-code if necessary

**Pitfall 3: Skipping the refactor phase**
- Solution: After tests pass, always look for improvements. Don't accumulate technical debt

**Pitfall 4: Not running tests frequently enough**
- Solution: Run tests after EVERY change. Make it automatic (watch mode, IDE integration)

**Pitfall 5: Writing tests that don't actually fail initially**
- Solution: Always verify the test fails before implementing. Catches test mistakes early

**Pitfall 6: Refactoring while tests are failing**
- Solution: Only refactor in the Green phase. If tests fail, fix them first or revert

## Quick Reference

**Red → Green → Refactor Cycle:**
1. 🔴 Write smallest failing test
2. ✅ Write simplest code to pass
3. 🔧 Improve structure while green
4. 🔁 Repeat

**Every iteration:**
- Write one test
- Make it run
- Improve structure
- Run all tests

**Before committing:**
- All tests passing
- No warnings
- Code refactored to quality standards

## Additional Resources

**Reference files:**
- **`references/test-patterns.md`** - Common test patterns and anti-patterns
- **`references/refactoring-catalog.md`** - Catalog of safe refactorings with examples

**Example files:**
- **`examples/tdd-session-transcript.md`** - Complete TDD session walkthrough
- **`examples/defect-fix-workflow.md`** - Example of fixing a bug with TDD

Consult these resources for detailed patterns and techniques when needed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kaneshin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
