---
name: xunit-test-framework
description: Execute and generate xUnit tests for C#/.NET projects with FluentAssertions and Moq support Use when this capability is needed.
metadata:
  author: fortiumpartners
---

# xUnit Test Framework

## Purpose

Provide xUnit test execution and generation for C#/.NET projects.

## Usage

```bash
dotnet run --project generate-test.csproj -- --source=Calculator.cs --output=CalculatorTests.cs --description="Division by zero"
dotnet test --filter=CalculatorTests
```

## Output Format

JSON with success, passed, failed, total, and failures array.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fortiumpartners) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
