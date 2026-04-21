---
name: tdd-workflow
description: Apply a language-agnostic test-driven development workflow. Use for new features or bug fixes where tests are expected. Use when this capability is needed.
metadata:
  author: wdonofrio
---

# Test-Driven Development Workflow

Write tests first, then implement. This ensures code is testable and requirements are clear.

## TDD Cycle

### RED: Write a Failing Test

1. **Identify the smallest behavior to implement**
   - One test = one behavior
   - Start with the simplest case

2. **Write the test first**
   - Describe expected behavior in the test name
   - Set up preconditions (Arrange)
   - Call the code under test (Act)
   - Assert the expected outcome (Assert)

3. **Run the test—it should fail**
   - Verify it fails for the right reason
   - If it passes, the behavior already exists or the test is wrong

### GREEN: Make It Pass

1. **Write the minimum code to pass**
   - Don't over-engineer
   - It's OK if the code is ugly
   - Just make the test green

2. **Run the test—it should pass**
   - If it fails, fix the implementation
   - Don't change the test to match broken code

### REFACTOR: Improve the Code

1. **Clean up while tests are green**
   - Remove duplication
   - Improve naming
   - Extract methods/functions
   - Apply design patterns if appropriate

2. **Run tests after each change**
   - Refactoring should not change behavior
   - Tests catch accidental changes

3. **Repeat the cycle**
   - Add the next test
   - Continue until feature is complete

## Getting Started

### Step 1: Confirm the Test Setup

Before writing tests:
- What test framework does the project use?
- Where do tests live?
- How do you run tests?
- Are there existing patterns to follow?

Ask if unclear.

### Step 2: Identify Test Cases

Before coding, list the behaviors to test:

**Happy Path**
- What's the main success case?

**Edge Cases**
- Empty/null inputs
- Boundary values
- Large inputs

**Error Cases**
- Invalid inputs
- Missing dependencies
- Failure scenarios

### Step 3: Prioritize Test Cases

Start with:
1. Simplest happy path
2. Most critical edge case
3. Main error case
4. Remaining cases

## Test Quality Guidelines

### Good Tests Are

- **Fast**: Milliseconds, not seconds
- **Isolated**: No shared state between tests
- **Deterministic**: Same result every time
- **Readable**: Test name describes the scenario
- **Focused**: One behavior per test

### Good Tests Have

- **Clear arrange/act/assert structure**
- **Meaningful assertions** (not just "no exception")
- **Descriptive failure messages**
- **No logic** (no conditionals in tests)

### Avoid

- **Testing implementation details**: Test behavior, not internals
- **Over-mocking**: If you mock everything, you test nothing
- **Brittle tests**: Don't break on irrelevant changes
- **Slow tests**: Mock external dependencies

## When Tests Are Hard to Write

Difficulty writing tests often indicates design problems:

| Problem | Possible Cause | Solution |
|---------|---------------|----------|
| Too many mocks needed | Too many dependencies | Extract smaller components |
| Hard to set up state | Tight coupling | Use dependency injection |
| Testing private methods | Wrong abstraction | Test through public interface |
| Non-deterministic results | Hidden dependencies | Make dependencies explicit |

## Running Tests

Always provide:
- The exact command to run the tests
- Expected output for success
- How to run just the new tests

If tests can't be run:
- Explain why
- Suggest manual verification steps

## Reminders

- Reference `shared/guidelines/testing.md` for test quality standards
- Keep tests deterministic—mock time, randomness, external services
- Run tests after every change
- Commit working tests with the implementation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wdonofrio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
