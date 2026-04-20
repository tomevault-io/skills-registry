---
name: test-first
description: >- Use when this capability is needed.
metadata:
  author: astrosteveo
---

<!-- NOTE: This skill intentionally allows model invocation (auto-activation)
     so Claude applies TDD discipline during any implementation task. -->

# Test-First Development

When writing code — whether new features, bug fixes, or refactors — follow this sequence strictly.
When communicating with the user about tests or asking questions, lead with recommendations
and explain your reasoning in plain language.

## Process

1. **Understand what you are testing.** Read existing test files to learn the project's
   testing conventions, framework, and patterns. Do not guess. If no tests exist yet,
   identify the test framework from the project's dependencies and set up the test
   infrastructure first.

2. **Write the tests first.** Define the expected behavior through tests before writing
   any implementation. Cover:
   - The primary success path.
   - Edge cases relevant to the feature.
   - Error conditions and how they should be handled.

3. **Run the tests.** They should fail. If they pass before you have written implementation
   code, your tests are not meaningful — they are testing nothing or testing the wrong thing.
   Rewrite them.

4. **Write the minimum implementation** to make the tests pass. Do not add functionality
   beyond what the tests require.

5. **Run the tests again.** All tests must pass. If any fail, fix the implementation.
   Do not modify the tests to match incorrect behavior.

6. **Run the full test suite.** Ensure your changes have not broken existing functionality.
   If existing tests fail, investigate and fix the regression.

<rules>
- Never modify a test to make it pass. Tests define the contract for correct behavior —
  if you change a test to match wrong output, you are encoding a bug as expected behavior.
  Always fix the implementation instead.
- Never skip tests because something "seems too simple to test." Simple code breaks too,
  especially when other code changes around it. A quick test now saves a painful debugging
  session later.
- Never write implementation first and tests after. Writing tests after implementation leads
  to tests that merely confirm what was written rather than defining what should be built.
  After-the-fact tests miss edge cases because you unconsciously avoid testing your own blind spots.
- If you are unsure how to test something, say so. Ask the user for guidance rather than
  skipping the test.
</rules>

## Detecting the Test Environment

Before writing tests, always check:

- What test framework is installed (read the dependency manifest).
- Where tests live in this project (check for `test/`, `tests/`, `__tests__/`, `*_test.go`, etc.).
- How tests are run (check `package.json` scripts, `Makefile`, `pyproject.toml`, etc.).
- What patterns existing tests follow (read at least one existing test file if available).

Do not assume Jest, pytest, or any specific framework. Read the project configuration.

## Examples

<example title="Good test: tests behavior, not implementation">
// Testing a function that calculates shipping cost
test("free shipping for orders over $50", () => {
  const cost = calculateShipping({ subtotal: 75.00, weight: 2.5 });
  expect(cost).toBe(0);
});

test("flat rate shipping for orders under $50", () => {
const cost = calculateShipping({ subtotal: 30.00, weight: 2.5 });
expect(cost).toBe(5.99);
});

test("throws on negative subtotal", () => {
expect(() => calculateShipping({ subtotal: -10, weight: 1 }))
.toThrow("Subtotal must be positive");
});
</example>

<example title="Bad test: tests implementation details, brittle">
// This test is coupled to internal implementation — it will break
// if you refactor the internals even if the behavior stays correct
test("shipping", () => {
  const calculator = new ShippingCalculator();
  // Testing private state instead of observable behavior
  expect(calculator._thresholds).toEqual([50, 100, 200]);
  expect(calculator._calculateInternal(75)).toBe(0);
});
</example>

<example title="The red-green cycle in practice">
// Step 1: Write the test FIRST — it fails because isEmail doesn't exist yet
test("validates email format", () => {
  expect(isEmail("user@example.com")).toBe(true);
  expect(isEmail("not-an-email")).toBe(false);
  expect(isEmail("")).toBe(false);
});
// Run: FAIL ✗ — ReferenceError: isEmail is not defined
// This is correct. The test defines the contract.

// Step 2: Write the minimum implementation
function isEmail(input) {
return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(input);
}
// Run: PASS ✓ — All three assertions pass

// Step 3: Run the full test suite to check for regressions
</example>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/astrosteveo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
