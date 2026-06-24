---
name: test-writing
description: > Use when this capability is needed.
metadata:
  author: karmaloopai
---

# Test Writing Skill

## Overview
Create thorough test coverage for software components.

## Workflow

1. **Analyze Code**: Understand what needs testing
2. **Identify Test Cases**:
   - Happy path scenarios
   - Edge cases
   - Error conditions
   - Boundary values
3. **Write Tests**: Create test files following project conventions
4. **Verify**: Run tests to ensure they pass
5. **Document**: Add test descriptions

## Test Types

### Unit Tests
- Test individual functions/methods
- Mock dependencies
- Fast execution
- High coverage

### Integration Tests
- Test component interactions
- Real dependencies when possible
- Test data flow
- API contracts

### End-to-End Tests
- Test complete user workflows
- Real environment
- Critical paths
- User scenarios

## Test Quality Standards

- Clear test names describing what is tested
- Arrange-Act-Assert pattern
- One assertion per test (generally)
- Independent tests (no shared state)
- Fast and reliable

## Tools

- `view`: To read code being tested
- `create_file`: To create new test files
- `run_terminal_command`: To execute test suites
- `grep_search`: To find existing tests

## Best Practices

- Test behavior, not implementation
- Cover edge cases thoroughly
- Keep tests maintainable
- Use descriptive test names
- Don't test framework code
- Mock external dependencies

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karmaloopai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
