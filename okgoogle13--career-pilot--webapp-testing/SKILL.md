---
name: webapp-testing
description: Runs or writes Playwright tests for the 'careercopilot' webapp. Use when asked to 'run playwright' or 'write a new e2e test'.
metadata:
  author: okgoogle13
---

# WebApp Testing Workflow

1.  Ask the user if they want to "run" existing tests or "write" a new test.

2.  **If "run":**
    - Ask for the test command (default: `yarn playwright test`).
    - Run the command and report the full output.
    - If it fails, suggest using the `root-cause-tracer` skill to debug the log.

3.  **If "write":**
    - Ask for a description of the test (e.g., "test the login flow").
    - Ask for the new test file name (e.g., `login.spec.ts`).
    - **Consult `references/careercopilot-selectors.md`** to use stable `data-testid` selectors.
    - Write the new Playwright test code to `e2e/{{FILE_NAME}}`.
    - Report success and ask the user if they want to run the new test.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/okgoogle13) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
