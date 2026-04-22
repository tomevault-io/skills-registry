---
name: check-test-logical-resp-claude
description: Audits passed tests only: checks that each model response is logically consistent with the user's question (topic, entities, numbers, intent). Does not re-check assertions; adds a semantic-sanity layer so tests that pass on keywords/tools but give off-topic or contradictory answers are surfaced. Resolves JSON like analyze-test-json (test-results_*.json, multi-model-results_*.json in src/IntegrationTesterApp/test-results/). Use when the user says /check-test-logical-resp, "check logical responses", "check test responses make sense", "audit passed tests for logic", or @-mentions a test-result JSON and asks whether responses were logical. Use when this capability is needed.
metadata:
  author: dezverev
---

# Check Test Logical Response — Passed-Test Semantic Audit

Audits **passed** tests only: verifies that each model response is logically coherent with the prompt (topic, entities, intent). Assertions already passed; this skill adds a "semantic sanity" layer so tests that pass on keywords/tools but give off-topic or contradictory answers are surfaced.

## When to Apply

- User says `/check-test-logical-resp`, "check logical responses", "check test responses make sense", "audit passed tests for logic"
- User @-mentions a `test-results_*.json` or `multi-model-results_*.json` and asks whether responses were logical or made sense
- User wants to know if any passed tests had nonsensical or off-topic answers

## File Resolution

Use the **same file resolution as the analyze-test-json skill**:

- **Base directory**: `src/IntegrationTesterApp/test-results/`
- **No path or "latest"**: Glob for `test-results_*.json` and `multi-model-results_*.json` under that directory. Parse the embedded timestamp `_yyyyMMdd_HHmmss` from each filename. Sort by timestamp descending; take the first file as latest.
- **Explicit path**: Use that path. If the user gives a bare name (e.g. `test-results_20260127_054623`), resolve to `src/IntegrationTesterApp/test-results/<name>.json`.

**Format detection**:

- Root has `modelResults` → **multi-model** (each `modelResults[i]` has `model`, `testResults`).
- Root has `testResults` → **single-run** (root-level `testResults`).

If the chosen path does not exist, report that and suggest running tests or checking the path.

## Per-Test Shape (JSON)

Same as analyze-test-json. Each item in `testResults` (or `modelResults[].testResults`) has (camelCase):

- **testCase**: `name`, `prompt`, `conversationHistory`, `expectedToolsToCall`, `expectedToolsNotToCall`, `responseMustContain`, `responseMustContainAny`, `tags`, …
- **passed**: boolean
- **response**: LLM response text
- **functionCallDetails**: `{ functionName, pluginName, name, parameters, result, resultString, nestedCalls? }[]`
- **failures**, **error**, **durationMs**, **timedOut**: as in analyze-test-json

## Scope: Passed Tests Only

- **Input**: All test results from the chosen file (single-run: root `testResults`; multi-model: each `modelResults[i].testResults`).
- **In scope for logical-response check**: Only tests where **`passed === true`**. Failed tests are already handled by assertions; this skill targets "passed but nonsensical."

For each passed test, use: `testCase.prompt`, `testCase.conversationHistory` (if any), `response`, and optionally `functionCallDetails`, then judge logical coherence against the criteria below.

## Logical Sense Criteria

Evaluate each **passed** test against these. Flag when one or more are violated. (`responseMustContain` / `responseMustContainAny` already passed — the check is overall coherence and correctness, not keyword presence.)

| Criterion | Meaning | Flag when … |
|-----------|--------|-------------|
| **Topic match** | Response should address the same subject as the prompt (e.g. stocks vs weather vs math). | User asked about weather and the reply is mainly about stocks. |
| **Entity match** | Named entities in the question (cities, tickers, numbers) should appear correctly in the response. | "Seattle" → response about another city; "MSFT" → reply about a different ticker. |
| **Intent alignment** | If the prompt asks for a specific kind of answer (e.g. "add 3 and 5", "get stock for AAPL"), the response should fulfill that intent. | User asked for a numeric result or stock info for AAPL; reply deflects or answers something else. |
| **No clear contradiction** | Response should not contradict the question. | User asks "weather in Seattle" and the model says "In New York the weather is …" without clearly addressing Seattle. |

## Workflow

1. **Resolve file**: Same as analyze-test-json (glob + latest by timestamp, or explicit path).
2. **Detect format**: Root has `modelResults` → multi-model; root has `testResults` → single-run.
3. **Collect passed tests**: From `testResults` (or from each `modelResults[i].testResults` for multi-model), keep only items with **`passed === true`**.
4. **For each passed test**:
   - Read `testCase.prompt`, `testCase.conversationHistory`, `response`, and optionally `functionCallDetails`.
   - Apply the logical-sense criteria above.
   - If the response is judged **not** logically coherent with the prompt, add it to a "Passed but illogical" list with: test name, model (if multi-model), prompt snippet, response snippet, and a short reason (e.g. "Response discusses stocks; user asked for Seattle weather").
5. **Summarize**: Count how many passed tests were reviewed and how many were flagged as illogical.
6. **Output**: Use the report template below.

## Output Report Template

Produce a markdown report in this form. When **M = 0** (no logical issues), omit the "Passed tests with logical response issues" detail; keep the summary.

```markdown
## Logical Response Check — Passed Tests

### 1. Run overview
- **File**: <resolved path>
- **Format**: Single-run | Multi-model
- **Model(s)**: <from configuration or modelResults[].model>
- **Passed tests reviewed**: <count>
- **Passed tests with logical issues**: <count>

### 2. Passed tests with logical response issues

For each passed test where the response did not make logical sense:

#### <testCase.name> [Model: <model> if multi-model]
- **Prompt** (snippet): "<prompt text>" [+ conversation context if present]
- **Response** (snippet): "<response excerpt that shows the problem>"
- **Issue**: <short explanation: topic mismatch, wrong entity, contradiction, or intent not fulfilled>

### 3. Summary
- <N> passed tests reviewed for logical coherence with the user's question.
- <M> showed logical response issues (listed above).
- If M is 0: All reviewed passed tests had responses that made logical sense given the prompt.
```

## Relation to Other Skills

| Skill | Relationship |
|-------|--------------|
| **analyze-test-json** | Focuses on **failing** tests (flow, assertions, recommendations). check-test-logical-resp reuses its file resolution and JSON shape, but focuses on **passed** tests and semantic/logical coherence of the response vs the prompt. |
| **smart-test-runner** | Runs tests and prints console output; it does not consume JSON. Either skill can be used after a run: smart-test-runner to run, then check-test-logical-resp on the generated JSON to audit passed-test logic. |
| **add-plugin-test** | Can add new tests (e.g. stricter keywords or new cases) if logical-response issues suggest missing or weak assertions. |

## Usage Examples

| Command / request | Action |
|-------------------|--------|
| `/check-test-logical-resp` or "check logical responses" | Resolve latest file in `test-results/`, audit all passed tests for logical coherence. |
| "audit passed tests for logic" | Same — resolve latest, then run logical-response check on passed tests only. |
| User @-mentions `test-results_20260127_054623.json` and asks "do these responses make sense?" | Resolve to that file (or under test-results/), run logical check on its passed tests. |
| "check test responses make sense" with no file | Resolve latest test-result JSON, run the audit. |

## File Locations & References

- **Test result directory**: `src/IntegrationTesterApp/test-results/`
- **File resolution**: Same as the analyze-test-json skill — glob `test-results_*.json` and `multi-model-results_*.json`, parse `_yyyyMMdd_HHmmss`, sort descending, take first for "latest".

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
