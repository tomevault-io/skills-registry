---
name: write-tests
description: Write comprehensive tests following the Analyze > Strategy > Implement > Verify workflow. Use when adding tests, improving coverage, or when the user asks to write tests for specific code. Use when this capability is needed.
metadata:
  author: packbot
---

# Write Tests: $ARGUMENTS

## Phase 1: Analyze

- Read the code to test: **$ARGUMENTS**
- Identify all public functions, methods, and APIs
- Map out the behavior: inputs > processing > outputs/side effects
- List edge cases: null/undefined, empty collections, boundary values, error conditions
- Check existing tests — extend coverage, don't duplicate

## Phase 2: Strategy

Determine the right test types:
- **Unit tests**: For pure functions, business logic, utilities
- **Integration tests**: For code that interacts with databases, APIs, file system
- **E2E tests**: For critical user workflows (use sparingly)

Test naming convention:
```
describe("ModuleName", () => {
  describe("functionName", () => {
    it("should [expected behavior] when [condition]", () => {
```

## Phase 3: Implement

Write tests following this priority:
1. **Happy path** — The main use case works correctly
2. **Edge cases** — Boundary values, empty inputs, null handling
3. **Error cases** — Invalid inputs, network failures, permission errors
4. **Integration points** — Interactions with external systems (mocked at boundaries)

Guidelines:
- Each test tests ONE behavior
- Tests are independent — no shared mutable state, no execution order dependencies
- Use descriptive assertion messages
- Mock at system boundaries (DB, API, file system), not internal functions
- Arrange > Act > Assert pattern in every test

## Phase 4: Verify

- [ ] All new tests pass: `{{TEST_COMMAND}}`
- [ ] All existing tests still pass
- [ ] Tests fail for the right reason when the implementation is broken
- [ ] No test depends on another test's execution
- [ ] No hardcoded test data that could become stale
- [ ] Tests cover happy path, edge cases, and error cases

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/packbot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
