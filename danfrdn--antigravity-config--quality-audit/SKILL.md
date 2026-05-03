---
name: quality-audit
description: Use when working with a strict quality gate to run AFTER code implementation is complete. Audits for security, correctness, simplicity, data integrity, and performance.
metadata:
  author: danfrdn
---

# Quality Audit Skill

Use this skill when the user indicates a task is "done" or specifically asks for an audit/review. This is a strict verification step, not a brainstorming session.

## Audit Checklist

Assess the code against the following combined standards. If CLI tools (like `ruff`, `bandit`, or `mypy`) are available, use them to validate your findings.

### 1. Correctness & Functionality
* **Correctness**: Does the code actually do what the prompt asked?
* **Edge cases**: Are error conditions (network failures, invalid input, empty states) handled gracefully?
* **Logic**: Are there any logical fallacies or infinite loops?

### 2. Security & Integrity
* **Security**: Are there vulnerabilities (SQLi, XSS, hardcoded secrets)?
* **Data Integrity**: Are type hints used? Is data validated at boundaries (API inputs/database reads)?

### 3. Code Health (Cleanliness & Linting)
* **Style**: Does it follow project conventions (PEP8, etc.)?
* **Cleanliness**: Is dead code, debug print statements, or commented-out logic removed?
* **Linting**: Are there unused imports or undefined variables?

### 4. Simplicity & Maintainability
* **Simplicity**: Is the code easy to read? (Low Cyclomatic Complexity).
* **Best Practices**: Is the code modular? Are functions small and single-purpose?

### 5. Performance
* **Efficiency**: Are there obvious inefficiencies (e.g., N+1 queries, nested loops on large data)?
* **Resource Usage**: Are connections/files closed properly?

## How to Report

1.  **Status**: Start with a strictly formatted status: `AUDIT RESULT: [PASS / FAIL / WARN]`.
2.  **The "Fix-it" List**: For every failure, provide:
    * The file/line number.
    * The specific violation (e.g., "Security: Hardcoded API key found").
    * A corrected code snippet (refactored version).
3.  **Tone**: Be objective and critical. Do not compliment the code; focus on hardening it.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danfrdn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
