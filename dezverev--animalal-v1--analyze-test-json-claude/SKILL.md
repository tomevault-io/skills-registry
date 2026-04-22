---
name: analyze-test-json-claude
description: Analyzes IntegrationTesterApp test-result JSON (test-results_*.json, multi-model-results_*.json) and produces in-depth analysis of flow (prompt → tools called → response → assertions) and responses, plus actionable fix recommendations. Resolves "latest" or no path to the newest file in src/IntegrationTesterApp/test-results/. Use when the user says /analyze-test-json, "analyze test json", "analyze test results", "analyze latest test results", or @-mentions a test-results or multi-model-results JSON file and asks for analysis or recommendations.
metadata:
  author: dezverev
---

# Analyze Test JSON — Flow & Response Analysis

Analyzes IntegrationTesterApp test-result JSON files to explain what happened per test (flow: prompt → tools called → response → assertion outcomes) and to surface cross-test patterns and concrete fix recommendations.

## When to Apply

- User says `/analyze-test-json`, `/analyze-test-json latest`, `/analyze-test-json <path>`, or "analyze test json", "analyze test results", "analyze latest test results"
- User @-mentions a specific `test-results_*.json` or `multi-model-results_*.json` and asks for analysis or recommendations
- User wants to understand why tests failed or how tool-call flow and response text relate to assertions

## File Resolution

**Base directory**: `src/IntegrationTesterApp/test-results/`

| User input | Action |
|------------|--------|
| No path, or "latest" | **Do not** rely on listing the directory (it can truncate). Use a **glob** for all result files: `test-results_*.json` and `multi-model-results_*.json` under the base directory. From the matched paths, parse the embedded timestamp in each filename (`_yyyyMMdd_HHmmss` before `.json`). Sort by that timestamp **descending** and pick the **first** file — that is the latest. Example: `test-results_20260127_054623.json` (20260127054623) is newer than `multi-model-results_20260126_083048.json` (20260126083048). |
| Explicit path | Use that path. If relative, resolve from repo root. If the user gives a bare name like `test-results_20260127_054623`, resolve to `src/IntegrationTesterApp/test-results/test-results_20260127_054623.json` (add base dir and `.json` when missing). |

**Supported formats**:

- **Single-run**: `test-results_{timestamp}.json` → root is `TestRunResults` with `configuration`, `summary`, `testResults`.
- **Multi-model**: `multi-model-results_{timestamp}.json` → root is `MultiModelTestResults` with `modelResults[]`; each entry has `model`, `summary`, `testResults`.

If the chosen path does not exist, report that and suggest running tests or checking the path.

## Per-Test Shape (from JSON)

Each item in `testResults` (or `modelResults[].testResults`) has this shape (camelCase in JSON):

- **testCase**: `name`, `prompt`, `conversationHistory`, `expectedToolsToCall`, `expectedToolsNotToCall`, `responseMustContain`, `responseMustContainAny`, `tags`, …
- **passed**: boolean
- **response**: LLM response text
- **functionCallDetails**: `{ functionName, pluginName, name, parameters, result, resultString, nestedCalls? }[]`
- **failures**: list of failure strings from TestRunner
- **error**: if exception occurred
- **durationMs**: int
- **timedOut**: boolean

## Failure Strings (TestRunner Verification)

The agent matches `failures[]` to these patterns to classify assertions:

| Pattern (substring or full) | Meaning |
|-----------------------------|---------|
| `Expected tool '…' was not called` | A tool in `expectedToolsToCall` was never invoked. |
| `Tool '…' was called but should not have been` | A tool in `expectedToolsNotToCall` was invoked. |
| `Response should contain '…' but does not` | A keyword from `responseMustContain` was missing (AND condition). |
| `Response should contain at least one of […] but contains none` | No keyword from `responseMustContainAny` appeared (OR condition). |

## Analysis Workflow

1. **Resolve file**: Use "File Resolution" above.
2. **Detect format**: Inspect root keys. Presence of `modelResults` → multi-model; presence of `testResults` at root → single-run.
3. **Run overview**: From `configuration`/root and `summary` (or each `modelResults[].summary`), extract: file path, format type, model(s), pass/fail counts, total duration.
4. **Per-failing-test analysis**: For every test where `passed === false` (and per model in multi-model):
   - **Flow**: prompt (+ conversationHistory) → tools actually called (`functionCallDetails[].functionName`) → response snippet → assertion outcomes (`failures`).
   - **Expected vs actual tools**: List `testCase.expectedToolsToCall` / `expectedToolsNotToCall` vs `functionCallDetails[].functionName`. Highlight missing or forbidden calls.
   - **Response vs keywords**: Compare `response` to `responseMustContain` and `responseMustContainAny`; note which keywords are missing.
   - **Flow narrative**: One short paragraph: "User asked X; the model called A, B (expected C but did not call it); it answered with …; assertions failed because …."
5. **Cross-test patterns**: Aggregate across failing tests (and across models in multi-model). Examples:
   - Same assertion failing in many tests (e.g. same required tool never invoked).
   - One tool never invoked anywhere.
   - One model much worse than others; same prompt failing only for that model.
   - Same missing keyword across tests.
6. **Recommendations**: Concrete, actionable fixes. Target: `TestCaseDefinitions.cs`, plugin/kernel code, or test design. Examples:
   - "In TestCaseDefinitions.cs, test '…': add 'Foo' to `ResponseMustContainAny` or relax to avoid flakiness."
   - "Expected tool 'StockAgent.AskStockAgent' was never called — check plugin descriptions and routing in StandardKernel / agent registration."
   - "Tool 'WeatherAgent.AskWeatherAgent' was called but forbidden — tighten ExpectedToolsNotToCall or improve tool-choice prompts."
   - "Add a test in TestCaseDefinitions.cs for scenario X to prevent regression."

Prefer bullet lists and short paragraphs. When suggesting code or config changes, mention specific files (`TestCaseDefinitions.cs`, `StandardKernel.cs`, plugin files) and approximate areas (e.g. "around the test named …", "AddNestedKernelPlugins") where relevant.

## Output Format — Report Template

Use this structure. Omit sections that have no content (e.g. no failing tests → omit "Per-failing-test analysis" detail; no cross-model data → omit model comparison in patterns).

```markdown
## Test Results Analysis

### 1. Run overview
- **File**: <resolved path>
- **Format**: Single-run | Multi-model
- **Model(s)**: <from configuration or modelResults[].model>
- **Pass / Fail / Total**: <counts>
- **Total duration**: <ms or per-model if multi-model>

### 2. Per-failing-test analysis

For each failing test (and per model in multi-model when relevant):

#### <testCase.name> [Model: <model> if multi-model]
- **Prompt** (snippet): "<prompt text>" [+ conversation history if present]
- **Expected tools**: <expectedToolsToCall> | **Actual calls**: <functionCallDetails[].functionName>
- **Forbidden tools**: <expectedToolsNotToCall> | **Called anyway**: <list if any>
- **Required keywords** (ResponseMustContain): <list> | **In response**: ✓/✗ per keyword
- **Any-of keywords** (ResponseMustContainAny): <list> | **In response**: ✓/✗ per keyword
- **Failures**: <exact failure strings>
- **Flow**: <One short paragraph: prompt → tools called → response → why assertions failed.>

### 3. Cross-test patterns
- <Bullet list: same assertion failing in many tests; tools never invoked; one model much worse; recurring missing keywords; etc.>

### 4. Recommendations
- <Bullet list of concrete fixes: TestCaseDefinitions.cs changes, keyword adjustments, plugin/kernel fixes, new tests. Mention files and areas where relevant.>
```

## Usage Examples

| Command / request | Action |
|-------------------|--------|
| `/analyze-test-json` or "analyze latest test results" | Resolve latest file in `test-results/`, then run full analysis. |
| `/analyze-test-json latest` | Same as no path — use newest file in `test-results/`. |
| `/analyze-test-json src/IntegrationTesterApp/test-results/multi-model-results_20250126_120000.json` | Analyze that multi-model file. |
| User @-mentions `test-results_20250126_143022.json` and says "why did these fail?" | Treat as explicit path (or resolve under test-results/), then analyze and emphasize failures and recommendations. |

## File Locations & References

- **Test result directory**: `src/IntegrationTesterApp/test-results/`
- **Single-run filenames**: `test-results_{yyyyMMdd_HHmmss}.json`
- **Multi-model filenames**: `multi-model-results_{yyyyMMdd_HHmmss}.json`
- **Resolving "latest"**: Glob for `test-results_*.json` and `multi-model-results_*.json` in that directory; parse `yyyyMMdd_HHmmss` from each filename; sort descending; use the first. Do not rely on directory listing order or truncation.
- **TestCase definitions**: `src/IntegrationTesterApp/TestCaseDefinitions.cs` — adjust expected/forbidden tools and response keywords here.
- **Verification logic**: `src/IntegrationTesterApp/TestRunner.cs` — `VerifyTest` produces the failure strings above.
- **Result types**: `src/IntegrationTesterApp/TestCase.cs` — `TestResult`, `TestRunResults`, `ModelTestResults`, `MultiModelTestResults`, `TestCase`.

## Relation to Other Skills

- **smart-test-runner**: Runs tests and produces console output; it does not produce or parse JSON. Use analyze-test-json when the user has (or asks for) JSON result files and wants flow analysis and fix recommendations.
- **add-plugin-test**: Adds test cases to TestCaseDefinitions.cs. Recommendations from analyze-test-json may suggest new tests; the user can then use add-plugin-test to add them.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
