---
name: testing-best-practices
description: Guidelines for implementing effective and maintainable tests. Use when this capability is needed.
metadata:
  author: matrixfounder
---
# Testing Best Practices

## 1. Hierarchy & Strategy
- **E2E (End-to-End):** Mandatory for every task.
  - *Stub Stage:* Assert hardcoded values (verify structure).
  - *Impl Stage:* Assert real logic (verify behavior).
- **Unit:** Cover edge cases, error handling, and specific algorithms.
- **Regression:** ALWAYS run the full test suite before submitting.

## 2. Critical Rules
- **NO LLM MOCKING in Tests:** Do not mock OpenAI/Anthropic calls in standard tests. Use recorded responses (VCR.py) or separate integration environments.
- **Realism:** Minimize mocks. Test real component capability where possible.
- **Isolation:** Tests must be independent. Database state should be reset between tests.
- **Environment:** Use the project's virtual environment (e.g., `/opt/projects/.../venv`).

## 3. Naming & Structure
- **Clear Names:** `test_shoud_return_error_when_invalid_input`.
- **Organization:** Mirror source structure (`src/auth` -> `tests/auth`).
- **Docstrings:** specificy WHAT is being tested.
- **Reference Examples:**
  - Python: `examples/pytest_structure.py`
  - JavaScript: `examples/jest_structure.js`

## 4. Resources
- **Templates:** Use `assets/templates/test_boilerplate.py` to start new test files quickly.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matrixfounder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
