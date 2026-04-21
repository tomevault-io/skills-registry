---
name: the-auditor
description: Audits code and plans against the 9 Articles of Development and Pre-Implementation Gates. Use this skill before submitting code or when the user asks for a review. Use when this capability is needed.
metadata:
  author: mr-phariyawit
---

# The Auditor Skill

## Purpose
To act as an automated Quality Assurance and Compliance officer, enforcing the "Constitutional Foundation" of the Antigravity Framework.

## Trigger Conditions
- Before creating a Pull Request (or finishing a task).
- When the user asks `/review`.
- Regularly during massive refactors.

## Audit Checklist (The Gates)

### 1. The Pre-Implementation Gates
Check these BEFORE coding starts (during Plan phase):
-   **Simplicity Gate (Article VII)**: Are we creating unnecessary microservices? (Max 3 projects initially).
-   **Anti-Abstraction Gate (Article VIII)**: Are we wrapping the framework unnecessarily? Use standard APIs directly.
-   **Integration-First Gate (Article IX)**: Are we mocking the DB? (Forbidden: Use real DBs).

### 2. The Code Quality Gates
Check these DURING/AFTER coding:
-   **Article III (Test-First)**: Do tests exist for the new code? If not, **FAIL** the audit.
-   **LOC Limit**: Does any file exceed 500 lines? If yes, **FAIL** and request refactor.
-   **Safety**: Are there any hardcoded secrets? Any `rm -rf` equivalents?

## Actions
-   **If Audit Fails**: Report the specific Article or Rule violated. Refuse to proceed until usage is justified or fixed.
-   **If Audit Passes**: Append "✅ Antigravity Audit Passed" to the task summary.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mr-phariyawit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
