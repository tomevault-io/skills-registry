---
name: test-writing-best-practices
description: Guide for writing effective unit and integration tests with best practices, including performance guidelines where tests under 1 minute are unit/integration, otherwise benchmark tests. Use when creating, reviewing, or optimizing test suites in GDScript/Godot projects. Use when this capability is needed.
metadata:
  author: horschig
---

# Test Writing Best Practices

This skill provides guidance for writing effective unit and integration tests in GDScript/Godot projects, with a focus on best practices and performance guidelines.

## Core Principles

### Test Classification by Execution Time

**Unit Tests**: Tests that verify individual functions/methods in isolation. Must complete in **under 1 minute**.

**Integration Tests**: Tests that verify interactions between multiple components. Must complete in **under 1 minute**.

**Benchmark Tests**: Any test that takes 1 minute or longer to execute. These should be separated from regular test suites and run separately.

### Best Practices

1. **Isolation**: Unit tests should mock dependencies and test one thing at a time. Use `add_child_autofree()` for test objects.

2. **Descriptive Names**: Test methods should start with `test_` and clearly describe what they're testing (e.g., `test_calculate_score_returns_expected_value`).

3. **Arrange-Act-Assert**: Structure tests with clear setup, execution, and verification phases.

4. **Edge Cases**: Test boundary conditions, null inputs, and error scenarios.

5. **Performance Awareness**: If a test approaches 1 minute, refactor or move to benchmarks.

6. **Cleanup**: Use `GutTest` lifecycle methods (`before_each`, `after_each`) for setup/teardown.

### GDScript Testing Patterns

```gdscript
extends GutTest

var service: LeagueService

func before_each():
    service = LeagueService.new()
    add_child_autofree(service)

func test_schedule_season_creates_correct_matches():
    var league = League.new()
    league.teams = [Team.new(), Team.new()]
    
    service.schedule_season(league)
    
    assert_eq(league.matches.size(), 2, "Should create home and away matches")
```

### Running Tests

Use GUT CLI for headless testing:
```bash
& "D:/Godot/C#/Godot_v4.3-stable_mono_win64.exe" --headless --path . -s addons/gut/gut_cmdln.gd -gdir=res://tests/unit/
```

### Performance Monitoring

- Track test execution times in CI/CD
- Flag tests exceeding 1 minute as benchmark candidates
- Use `--gtest` for individual test timing analysis

## When to Use This Skill

- Creating new test files
- Reviewing existing test coverage
- Optimizing slow test suites
- Setting up test infrastructure
- Debugging test failures

## References

See project documentation in `docs/` for specific testing guidelines and GUT framework usage.

## References\n\nSee the `godot-gut-test-execution` skill for practical command-line examples and a PowerShell helper to run single tests and directories.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/horschig) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
