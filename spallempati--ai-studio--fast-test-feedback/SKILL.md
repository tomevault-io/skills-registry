---
name: fast-test-feedback
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# Enforce Fast Feedback in Unit Test Execution

## Description

This rule enforces time-bounded unit test execution to ensure rapid feedback
during development. Unit test suites should complete within 5 minutes total. Any
individual test exceeding 500 milliseconds must be tagged and reported in CI.

## Purpose

To accelerate developer iteration cycles and reduce context-switching delays by
ensuring fast, focused unit tests. It also isolates slower tests for targeted
analysis and future optimization, contributing to continuous quality and CI
efficiency.

## Scope

- All unit test suites in Java (JUnit), .NET (xUnit/NUnit), Python (PyTest), or equivalent
- Applies to developer-run test suites and CI pipelines
- Enforced for pull requests, merges to protected branches
- Developers, QA engineers, and test maintainers

## SDLC Integration

- **Planning**: Test runtime requirements included in DoD
- **Analysis**: Timing metrics used to identify hotspots
- **Design**: Encourages fast and focused test structure
- **Development**: Slow tests must be explicitly tagged
- **Testing**: CI measures runtime and flags violations
- **Deployment**: Prevents regressions in test suite performance
- **Maintenance**: Test suite health tracked over time

## Standards

### Test Runtime and Tagging

- Full unit test suite **SHOULD** execute in ≤ 300 seconds
- Any test running > 500 ms **MUST** be tagged with `@slow`, `@performance`, or
  equivalent
- Slow tests **MUST** be reported in CI logs or dashboards
- Test runtime regressions **SHOULD** trigger review or optimization tasks

## Actionable Metrics

| Metric                | Target Value  | Measurement Method            | Enforcement Level |
| --------------------- | ------------- | ----------------------------- | ----------------- |
| Total test runtime    | ≤ 300 seconds | CI timer / test runner logs   | **SHOULD**        |
| % slow tests reported | 100 %         | Tag presence + duration check | **MUST**          |
| Untagged slow tests   | 0             | Linter or runtime linter      | **MUST**          |

## Implementation

### Configuration Requirements

- Use JUnit test runners with duration threshold logging
- Enforce tagging via custom linter or annotation scan
- Monitor runtime via test reports or CI timing metadata

#### Example: Correct Implementation (Java)

```java
@Tag("slow")
@Test
void generatesLargeReport() throws Exception {
    Thread.sleep(600); // Simulates 600ms logic
    ...
}
```

#### Example: Correct Implementation (C#/.NET with xUnit)

```csharp
// Tag slow tests with Trait
[Trait("Category", "Slow")]
[Fact]
public async Task GenerateLargeReport_LargeDataset_CompletesWithinTimeout()
{
    // Arrange
    var largeDataset = GenerateTestData(10000);

    // Act
    var result = await _reportService.GenerateAsync(largeDataset);

    // Assert
    Assert.NotNull(result);
}

// Run only fast tests (exclude slow)
// dotnet test --filter "Category!=Slow"

// Run only slow tests
// dotnet test --filter "Category=Slow"
```

### Test Timeout Configuration (.NET)

```csharp
// Set timeout for individual test
[Fact(Timeout = 5000)] // 5 seconds
public async Task ProcessOrder_ValidOrder_CompletesQuickly()
{
    // Test implementation
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
