---
name: tdd
description: Guide for Test-Driven Development methodology. Use when writing tests first, practicing red-green-refactor, implementing new features with test coverage, or when user mentions TDD workflow. Helps ensure code correctness through iterative test-code cycles. Use when this capability is needed.
metadata:
  author: schicks
---

# Test-Driven Development

This skill guides you through Test-Driven Development (TDD), a software development approach where tests drive the design and implementation of code.

## When to Use This Skill

- User explicitly asks for TDD or test-first development
- Implementing new features where correctness is critical
- Refactoring code while maintaining behavior guarantees
- Fixing bugs with regression test coverage
- User mentions "red-green-refactor" or similar TDD terminology

## The TDD Cycle

TDD follows a strict **Red-Green-Refactor** cycle:

### Red: Write a Failing Test

1. Write a single test that describes the desired behavior
2. Run the test to confirm it fails (for the right reason)
3. The test should fail because the code doesn't exist yet, not due to syntax errors

**Key principles:**
- Write the smallest test that moves you toward your goal
- Test behavior, not implementation details
- Use descriptive test names that explain the expected behavior

### Green: Make the Test Pass

1. Write the minimum code necessary to pass the test
2. Don't add functionality beyond what the test requires
3. It's okay to write "ugly" code at this stage—correctness comes first
4. **Check for linter and type errors**—green means ALL errors are resolved, not just passing tests

**Key principles:**
- Resist the urge to write more code than needed
- Don't optimize prematurely
- Focus solely on making the test pass
- A passing test with linter/type errors is NOT green—fix all errors before declaring the phase complete

### Refactor: Improve the Code

1. Clean up the code while keeping all tests green
2. Remove duplication between test and production code
3. Improve naming, structure, and clarity
4. Run tests after each small change

**Key principles:**
- Refactoring means changing structure without changing behavior
- Tests provide a safety net—use them
- Apply small, incremental improvements

## TDD Workflow in Practice

### Starting a New Feature

```
1. Identify the first small piece of behavior to implement
2. Write a test for that behavior
3. Run test → expect RED
4. Write minimal code to pass
5. Run test → expect GREEN
6. Refactor if needed
7. Repeat with next piece of behavior
```

### Fixing a Bug

```
1. Write a test that reproduces the bug (test should fail)
2. Confirm the test fails for the expected reason
3. Fix the bug with minimal code changes
4. Confirm the test passes
5. Check that no other tests broke
6. Refactor if appropriate
```

### Refactoring Existing Code

```
1. Ensure existing tests cover the behavior you're changing
2. Add tests for any uncovered behavior first
3. Make small refactoring changes
4. Run tests after each change
5. Continue until refactoring is complete
```

## Test Design Guidelines

### Good Tests Are

- **Fast**: Tests should run quickly to enable rapid feedback
- **Isolated**: Each test should be independent of others
- **Repeatable**: Same result every time, regardless of environment
- **Self-validating**: Pass or fail clearly without manual inspection
- **Timely**: Written before or alongside the code they test

### Test Structure

Follow the **Arrange-Act-Assert** (or Given-When-Then) pattern:

```
Arrange: Set up the test context and inputs
Act:     Execute the behavior being tested
Assert:  Verify the expected outcome
```

### What to Test

- Public interfaces and contracts
- Edge cases and boundary conditions
- Error handling and failure modes
- Business rules and validation logic

### Type Design Best Practices

When designing types, aim to make invalid states impossible to construct and be as parsimonious as possible.

- **All states should be meaningful**: If there is a value that fits in the type which should never occur, the type is badly designed.
- **Avoid redundant state**: If a process is complete, don't carry intermediate data into the terminal state—it's noise that makes one state look like many.
- **Use discriminated unions**: Each variant should contain only the data relevant to that state.
- **Don't duplicate names**: If a name is unambiguous in context, don't rename it just because you're adding another abstraction layer.

### What Not to Test

- Private implementation details
- Framework or library code
- Simple getters/setters with no logic
- Code that only delegates to tested code

## Common TDD Pitfalls

### Writing Too Much Test at Once

**Problem**: Large tests are harder to make pass and debug.
**Solution**: Start with the simplest possible test case.

### Writing Production Code Before Tests

**Problem**: Defeats the purpose of TDD; tests become afterthoughts.
**Solution**: Discipline yourself to write the test first, even if you "know" the implementation.

### Testing Implementation Instead of Behavior

**Problem**: Tests become brittle and break during refactoring.
**Solution**: Test what the code does, not how it does it.

### Skipping the Refactor Step

**Problem**: Technical debt accumulates; code quality degrades.
**Solution**: Treat refactoring as a required part of each cycle.

### Not Running Tests Frequently

**Problem**: Delayed feedback makes debugging harder.
**Solution**: Run tests after every small change.

## Working with the Agent

When using TDD with an AI coding agent:

1. **State your intent clearly**: "Let's implement X using TDD"
2. **Start small**: Begin with the simplest test case
3. **Verify each step**: Confirm tests fail/pass as expected before proceeding
4. **Ask for refactoring suggestions**: After green, ask "What could we refactor?"
5. **Review test coverage**: Periodically ask "What edge cases are we missing?"

### Pacing: Pause After Each Cycle

**CRITICAL**: TDD is collaborative. After completing each Red-Green-Refactor cycle, the agent MUST pause and wait for user input before starting the next cycle.

After reaching GREEN:

1. Show the user what changed (the test that was added and the code that makes it pass)
2. Ask if they want to refactor anything before moving on
3. Suggest potential refactoring opportunities if any exist
4. Wait for the user to indicate they're ready for the next test

**Do NOT** automatically continue to the next test case. The pause between cycles is essential for:

- Allowing the user to understand and internalize the changes
- Giving the user opportunity to suggest alternative approaches
- Discussing design decisions before they become entrenched
- Keeping the user engaged as an active participant, not a spectator
- Your code changes should speak for themselves. Do not summarize what you changed.

Example pause message:

> "The test passes. Before we continue, would you like to refactor anything? Or shall I suggest the next test case?"

## TDD Commands

Use these phrases to guide the agent:

- "Write a failing test for [feature]"
- "Make this test pass with minimal code"
- "Refactor this code while keeping tests green"
- "What's the next test case we should write?"
- "Add a test for [edge case]"
- "This test is too big—help me break it down"

## Quick Reference

| Phase | Goal | Constraint |
|-------|------|------------|
| Red | Define expected behavior | Test must fail |
| Green | Implement behavior | Minimal code only |
| Refactor | Improve quality | Tests must stay green |

Remember: TDD is about **confidence through rapid feedback**. Small steps, frequent test runs, and continuous refactoring lead to well-designed, correct code.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/schicks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
