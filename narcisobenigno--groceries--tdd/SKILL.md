---
name: tdd
description: Enforces test-driven development (TDD) following Kent Beck's methodology. MANDATORY micro-cycle approach - write ONE test, watch it fail, write minimal implementation, refactor, then NEXT test. NEVER write all tests first or implementation first. Use for ANY code writing task. Use when this capability is needed.
metadata:
  author: narcisobenigno
---

# Test-Driven Development (TDD) Enforcement

## CRITICAL RULE: ONE Test at a Time

**NEVER write implementation code before writing a test.**
**NEVER write multiple tests before writing implementation.**

The cycle is: **ONE test → implementation for that test → refactor → REPEAT**

When the user asks you to implement ANY code (features, bug fixes, new functions, deciders, projections, handlers), you MUST follow this EXACT sequence:

1. Write ONE test (just one!)
2. Run test → verify it FAILS (Red phase)
3. Write MINIMAL implementation to make THAT test pass (Green phase)
4. Run test → verify it PASSES
5. Refactor if needed while keeping tests passing (Refactor phase)
6. Run test → verify still passing
7. **GOTO step 1** for the NEXT test

**You are FORBIDDEN from:**
- ❌ Writing all tests first, then all implementation
- ❌ Writing complete implementation, then adding tests
- ❌ Writing multiple tests before any implementation
- ❌ Skipping the "run test and watch it fail" step

## When This Skill Applies

This skill is MANDATORY for:
- ✅ Implementing new features
- ✅ Creating new functions or modules
- ✅ Writing deciders or projections (event sourcing)
- ✅ Creating HTTP handlers
- ✅ Fixing bugs (write test that reproduces bug first)
- ✅ Refactoring (tests ensure behavior doesn't change)
- ✅ Adding business logic

This skill does NOT apply to:
- ❌ Configuration files (package.json, tsconfig.json)
- ❌ View templates (.ejs files)
- ❌ Documentation files (.md)
- ❌ Build scripts

## Project-Specific Test Patterns

### Test File Naming Convention

```
Implementation:  src/usecases/budget/allocate.ts
Test:           src/usecases/budget/allocate.spec.ts

Implementation:  src/products/add.ts
Test:           src/products/add.spec.ts
```

**Pattern:** Same directory, same name, `.spec.ts` extension

### Running Tests

```bash
# Run all tests in a package
bun test

# Run specific test file
node --import tsx --test 'src/usecases/budget/allocate.spec.ts'

# Run tests matching pattern
node --import tsx --test 'src/**/*.spec.ts'
```

### Event Sourcing Decider Tests

For deciders, test both `decide()` and `evolve()` functions:

```typescript
import { describe, it } from "node:test"
import assert from "node:assert"
import { AllocateBudget, type AllocateBudgetCommand } from "./allocate"

describe("AllocateBudget", () => {
  describe("decide", () => {
    it("should create budget.allocated event when budget does not exist", async () => {
      const decider = AllocateBudget()
      const command: AllocateBudgetCommand = {
        type: "budget.allocate",
        id: "budget_123",
        description: "Groceries",
        amount: 500,
        frequency: "monthly",
      }

      const events = await decider.decide(command, decider.initialState())

      assert.strictEqual(events.length, 1)
      assert.strictEqual(events[0].event.type, "budget.allocated")
      assert.strictEqual(events[0].event.id, "budget_123")
      assert.strictEqual(events[0].event.description, "Groceries")
      assert.strictEqual(events[0].event.amount, 500)
      assert.strictEqual(events[0].event.frequency, "monthly")
    })

    it("should return empty array when budget already exists (idempotency)", async () => {
      const decider = AllocateBudget()
      const command: AllocateBudgetCommand = {
        type: "budget.allocate",
        id: "budget_123",
        description: "Groceries",
        amount: 500,
        frequency: "monthly",
      }

      // First allocation
      const events = await decider.decide(command, decider.initialState())

      // Evolve state
      const state = decider.evolve(decider.initialState(), {
        position: 1n,
        timestamp: new Date(),
        streamId: ["budget_123"],
        type: "budget.allocated",
        event: events[0].event,
      })

      // Second allocation (should be idempotent)
      const secondEvents = await decider.decide(command, state)

      assert.strictEqual(secondEvents.length, 0)
    })
  })

  describe("evolve", () => {
    it("should mark budget as allocated in state", () => {
      const decider = AllocateBudget()
      const initialState = decider.initialState()

      const state = decider.evolve(initialState, {
        position: 1n,
        timestamp: new Date(),
        streamId: ["budget_123"],
        type: "budget.allocated",
        event: {
          type: "budget.allocated",
          id: "budget_123",
          description: "Groceries",
          amount: 500,
          frequency: "monthly",
        },
      })

      assert.strictEqual(state["budget_123"], true)
    })
  })
})
```

### Projection Tests

For projections, test the `catchup()` method and query methods:

```typescript
import { describe, it } from "node:test"
import assert from "node:assert"
import { eventstore } from "@groceries/event-sourcing"
import { InMemoryProjection } from "./in-memory-projection"
import type { BudgetEvent } from "./event"

describe("BudgetProjection", () => {
  it("should return empty array when no budgets exist", async () => {
    const store = new eventstore.InMemory<BudgetEvent>()
    const projection = InMemoryProjection(store)

    await projection.catchup()
    const budgets = await projection.all()

    assert.strictEqual(budgets.length, 0)
  })

  it("should return allocated budget after catchup", async () => {
    const store = new eventstore.InMemory<BudgetEvent>()
    await store.save([{
      streamId: ["budget_123"],
      type: "budget.allocated",
      event: {
        type: "budget.allocated",
        id: "budget_123",
        description: "Groceries",
        amount: 500,
        frequency: "monthly",
      },
    }])

    const projection = InMemoryProjection(store)
    await projection.catchup()
    const budgets = await projection.all()

    assert.strictEqual(budgets.length, 1)
    assert.strictEqual(budgets[0].id, "budget_123")
    assert.strictEqual(budgets[0].description, "Groceries")
    assert.strictEqual(budgets[0].amount, 500)
    assert.strictEqual(budgets[0].frequency, "monthly")
  })

  it("should return budget by id", async () => {
    const store = new eventstore.InMemory<BudgetEvent>()
    await store.save([{
      streamId: ["budget_123"],
      type: "budget.allocated",
      event: {
        type: "budget.allocated",
        id: "budget_123",
        description: "Groceries",
        amount: 500,
        frequency: "monthly",
      },
    }])

    const projection = InMemoryProjection(store)
    await projection.catchup()
    const budget = await projection.byId("budget_123")

    assert.strictEqual(budget.id, "budget_123")
    assert.strictEqual(budget.description, "Groceries")
  })

  it("should throw error when budget not found", async () => {
    const store = new eventstore.InMemory<BudgetEvent>()
    const projection = InMemoryProjection(store)
    await projection.catchup()

    await assert.rejects(
      async () => await projection.byId("budget_nonexistent"),
      { name: "BudgetNotFoundError" }
    )
  })
})
```

## TDD Workflow (Kent Beck Style)

**CRITICAL: Work on ONE test at a time. NEVER write multiple tests before implementation.**

### The Micro-Cycle (Repeat This For Each Test)

#### Step 1: Write ONE Test (Just One!)

Write a SINGLE test case. Not multiple tests. Just one.

```typescript
// CORRECT: Write ONE test
it("should create budget.allocated event when budget does not exist", async () => {
  // test code here
})

// WRONG: Don't write multiple tests at once
it("should create event...", async () => { })
it("should be idempotent...", async () => { })  // ❌ STOP! One at a time!
it("should reject negative...", async () => { })  // ❌ NO!
```

#### Step 2: Run the Test (Watch It Fail - RED)

Run ONLY this test. It MUST fail. This proves the test is actually testing something.

```bash
node --import tsx --test 'src/usecases/budget/allocate.spec.ts'
```

Expected output: ❌ Test fails (RED)

If it passes without implementation, the test is wrong or testing the wrong thing.

#### Step 3: Write Minimal Implementation (Make It Pass - GREEN)

Write the SMALLEST amount of code to make THIS ONE test pass. Don't think about future tests.

```bash
node --import tsx --test 'src/usecases/budget/allocate.spec.ts'
```

Expected output: ✅ Test passes (GREEN)

#### Step 4: Refactor (Keep It GREEN)

Only now, improve the code:
- Remove duplication
- Improve names
- Simplify logic

After EACH change, run tests to ensure they still pass.

#### Step 5: Repeat the Cycle

Now go back to Step 1 and write THE NEXT test. One test at a time.

### Planning Tests (Mental Note Only)

You may mentally list what needs testing:
```
Mental checklist (don't write these tests yet):
- create event when doesn't exist ← Start here (write this test first)
- idempotent behavior ← Write this test AFTER first passes
- reject negative amounts ← Write this test AFTER second passes
- reject invalid frequency ← Write this test AFTER third passes
```

But write code for only ONE at a time.

## Anti-Patterns (What NOT to Do)

### ❌ WRONG: Writing Implementation First

```typescript
// DON'T DO THIS - No test written first!
export function AllocateBudget(): Decider<...> {
  return {
    decide: async (command, state) => {
      // Implementation without tests
    }
  }
}
```

### ✅ RIGHT: Writing Test First

```typescript
// Step 1: Write the test FIRST
describe("AllocateBudget", () => {
  it("should create budget.allocated event", async () => {
    const decider = AllocateBudget()
    const events = await decider.decide(command, state)
    assert.strictEqual(events.length, 1)
  })
})

// Step 2: Run test (it fails - good!)
// Step 3: Write minimal implementation to pass
```

### ❌ WRONG: Skipping Test Verification

Don't assume tests pass. Always run them:

```bash
# ALWAYS run tests to verify
bun test
```

### ❌ WRONG: Writing All Tests First, Then Implementation

```
❌ write allocate.spec.ts (ALL tests at once)
   - test 1 ❌
   - test 2 ❌
   - test 3 ❌
   - test 4 ❌
❌ write allocate.ts (complete implementation)
   - all tests pass ✅
```

**Why this is wrong:** You lose the benefit of incremental design. Tests should guide your implementation one step at a time.

### ❌ WRONG: Writing All Code, Then All Tests

```
❌ write allocate.ts (complete implementation)
❌ write projection.ts (complete implementation)
❌ write allocate.spec.ts (all tests)
❌ write projection.spec.ts (all tests)
```

**Why this is wrong:** Tests written after implementation are just checking existing behavior, not driving design.

### ✅ RIGHT: Micro-Cycle - ONE Test, Minimal Code, Repeat

```
Cycle 1:
✅ write allocate.spec.ts (ONLY test 1: "should create event")
✅ run test → ❌ FAILS (good!)
✅ write allocate.ts (minimal code to pass test 1)
✅ run test → ✅ PASSES
✅ refactor if needed
✅ run test → ✅ still passes

Cycle 2:
✅ write allocate.spec.ts (add ONLY test 2: "should be idempotent")
✅ run test → ❌ FAILS (good!)
✅ update allocate.ts (minimal code to pass test 2)
✅ run test → ✅ PASSES
✅ refactor if needed
✅ run test → ✅ still passes

Cycle 3:
✅ write allocate.spec.ts (add ONLY test 3: "should reject negative amounts")
✅ run test → ❌ FAILS (good!)
✅ update allocate.ts (minimal code to pass test 3)
✅ run test → ✅ PASSES

... repeat for each remaining test
```

**Why this is right:** Each test drives exactly the code needed. Implementation emerges from requirements.

## Checklist Before Completing Any Task

Before marking a coding task as complete, verify you followed the micro-cycle for EACH test:

**For EACH test case, did you:**
- [ ] Write exactly ONE test (not multiple)
- [ ] Run the test and verify it FAILED (Red)
- [ ] Write MINIMAL implementation to pass THAT test
- [ ] Run the test and verify it PASSED (Green)
- [ ] Refactor (if needed)
- [ ] Verify tests still pass after refactoring
- [ ] Then move to NEXT test (repeat above)

**Overall checklist:**
- [ ] Test file created with `.spec.ts` extension
- [ ] Followed Red-Green-Refactor for every single test
- [ ] No existing tests broken
- [ ] Edge cases covered (empty arrays, null values, errors)
- [ ] Idempotency tested (for deciders)
- [ ] All tests run and pass

**Red flags (indicates you did NOT follow TDD):**
- 🚩 You wrote more than one test before implementation
- 🚩 You wrote implementation before any tests
- 🚩 You didn't run tests to see them fail
- 🚩 You wrote "complete" implementation in one go

## Test Assertion Library

Use Node.js built-in `assert` module:

```typescript
import assert from "node:assert"

// Equality checks
assert.strictEqual(actual, expected)
assert.deepStrictEqual(actualObject, expectedObject)
assert.notStrictEqual(actual, notExpected)

// Boolean checks
assert.ok(value)
assert.strictEqual(value, true)

// Array/object checks
assert.strictEqual(array.length, 3)
assert.deepStrictEqual(obj, { id: "123", name: "Alice" })

// Error checks
await assert.rejects(
  async () => await functionThatThrows(),
  { name: "ExpectedErrorName" }
)

assert.throws(
  () => functionThatThrows(),
  /expected error message/
)
```

## Test Organization Structure

```typescript
import { describe, it } from "node:test"
import assert from "node:assert"

describe("Feature/Module Name", () => {
  describe("specific function or method", () => {
    it("should [expected behavior] when [condition]", () => {
      // Arrange: Set up test data
      const input = { ... }

      // Act: Call the function
      const result = functionUnderTest(input)

      // Assert: Verify the result
      assert.strictEqual(result, expectedValue)
    })
  })
})
```

## Examples from Codebase

Reference existing test files as examples:
- `apps/app/src/usecases/product/add.spec.ts` - Decider tests
- `apps/app/src/usecases/product/in-memory-projection.spec.ts` - Projection tests
- `apps/app/src/usecases/product/change-name.spec.ts` - Event evolution tests

## Summary: The TDD Mantra

**ONE Test → Red → Green → Refactor → REPEAT**

1. **ONE Test**: Write exactly ONE test case
2. **Red**: Run it, watch it fail
3. **Green**: Write minimal code to make THAT test pass
4. **Refactor**: Clean up while keeping tests green
5. **REPEAT**: Go back to step 1 for the NEXT test

**The Golden Rules:**
- Write ONE test at a time (never batch tests)
- Always watch tests fail before writing implementation
- Write the simplest code that passes the current test
- Refactor only after tests pass
- Tests are specifications, not documentation

**What TDD is NOT:**
- ❌ Writing all tests, then all implementation
- ❌ Writing implementation, then retrofitting tests
- ❌ Writing multiple tests before any implementation

**What TDD IS:**
- ✅ Tiny cycles: one test → code → refactor → next test
- ✅ Tests drive design incrementally
- ✅ Each test adds ONE new behavior

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/narcisobenigno) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
