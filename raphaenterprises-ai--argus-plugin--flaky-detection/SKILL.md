---
name: flaky-detection
description: Detect and analyze flaky tests that fail intermittently. Use this skill when a test fails unexpectedly, the user asks about flaky tests, or test results are inconsistent. Use when this capability is needed.
metadata:
  author: raphaenterprises-ai
---

# Flaky Test Detection

This skill identifies and analyzes flaky tests - tests that pass and fail intermittently without code changes.

## When to Use

- Test fails unexpectedly after passing previously
- User asks "is this test flaky?"
- User asks "why does this test fail sometimes?"
- Test results are inconsistent across runs
- User wants to identify unreliable tests

## How It Works

1. Query test execution history for the failing test
2. Analyze pass/fail patterns over time
3. Identify flakiness indicators:
   - Timing-dependent failures
   - Race conditions
   - External dependencies
   - Resource contention
4. Calculate flakiness score
5. Suggest remediation strategies

## Implementation

Use the following Argus MCP tools:

1. **`argus_quality_stats`** - Get test quality metrics and flakiness scores
   - Input: `{ "project_id": "<project>", "test_id": "<test>" }`
   - Returns: Flakiness score, failure patterns, execution history

2. **`argus_healing_patterns`** - Find common fix patterns for flaky tests
   - Input: `{ "failure_type": "flaky", "test_id": "<test>" }`
   - Returns: Suggested fixes based on similar resolved issues

## Output Format

### Flakiness Analysis

| Test | Flakiness Score | Pattern | Suggested Fix |
|------|-----------------|---------|---------------|
| test_async_upload | 🔴 0.73 | Timing | Add explicit wait for upload completion |
| test_db_connection | 🟡 0.45 | Resource | Use connection pooling |
| test_api_response | 🟢 0.12 | None | Likely not flaky |

### Flakiness Indicators

- **Timing Issues**: Test depends on async operations without proper waits
- **Race Conditions**: Multiple threads/processes competing for resources
- **External Dependencies**: Network, databases, or third-party services
- **State Leakage**: Previous tests affecting current test state

### Remediation Steps

1. Add explicit waits/retries for async operations
2. Isolate test state with fixtures
3. Mock external dependencies
4. Use deterministic test data

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/raphaenterprises-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
