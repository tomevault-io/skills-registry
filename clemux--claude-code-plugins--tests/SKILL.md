---
name: tests
description: Run pytest with coverage and baseline tracking. Use when the user invokes /tests. Use when this capability is needed.
metadata:
  author: clemux
---

# /tests — Run Pytest with Coverage & Baseline Tracking

The user has invoked `/tests`. Run the project's test suite with coverage, compare results against a local baseline history, and report regressions, improvements, and trends.

## Instructions

1. Extract pytest arguments (everything after `/tests` in the arguments). These are referred to as `{PYTEST_ARGS}` below.
2. Use the **Task** tool to launch the `test-runner` subagent with `model: "haiku"`. The prompt **MUST** include the compare script path. Use this format:
   - With args: `"Run tests with args: {PYTEST_ARGS}. Compare script: ${CLAUDE_PLUGIN_ROOT}/scripts/compare_results.py"`
   - Without args: `"Run tests. Compare script: ${CLAUDE_PLUGIN_ROOT}/scripts/compare_results.py"`
3. When the subagent returns, relay its result back to the user **without modification**.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/clemux) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
