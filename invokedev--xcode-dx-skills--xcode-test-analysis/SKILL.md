---
name: xcode-test-analysis
description: Analyzes xcresult bundles to provide insights on test failures, slow tests, and potentially flaky tests. Use when user asks about test results, test failures, slow tests, or test coverage.
metadata:
  author: invokedev
---

# Xcode Test Analysis

Parses `.xcresult` bundles to provide detailed test insights.

## When to Use

- User asks "why did my tests fail?"
- User wants to identify slow tests
- User suspects flaky tests
- User wants test coverage information

## Usage

```bash
swift ~/.claude/plugins/xcode-dx-skills/skills/xcode-test-analysis/scripts/parse-xcresult.swift /path/to/Test.xcresult
```

Or invoke directly:

```
/xcode-test-analysis path/to/Test.xcresult
```

## Finding xcresult Bundles

xcresult bundles are typically located at:

```
~/Library/Developer/Xcode/DerivedData/<project>/Logs/Test/
```

Or run tests with explicit result bundle path:

```bash
xcodebuild test -scheme MyApp -resultBundlePath ./TestResults.xcresult
```

## What Gets Analyzed

| Metric | Description |
|--------|-------------|
| **Failed tests** | Test names, failure messages, file/line |
| **Slow tests** | Tests exceeding duration threshold |
| **Test duration** | Timing for each test case |
| **Flaky candidates** | Tests with inconsistent results (if multiple runs available) |
| **Coverage** | Code coverage percentage (if enabled) |

## Output Format

The script outputs JSON with:
- `summary`: Pass/fail counts, total duration
- `failures`: Detailed failure information with messages
- `slow_tests`: Tests sorted by duration
- `by_suite`: Results grouped by test suite
- `coverage`: Coverage data if available

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/invokedev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
