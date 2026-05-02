---
name: code-review
description: systematic code review for correctness, security, style, and performance. Use this when the user asks for a review or before committing significant changes. Use when this capability is needed.
metadata:
  author: splashcodedex
---

# Code Review Skill

This skill provides a structured approach to reviewing code, ensuring high quality, security, and adherence to project standards (as defined in `tech_standards.md`).

## Verification Hierarchy
Review the code in this specific order of priority:

1.  **Correctness & Logic** (Critical)
    -   Does the code actually solve the problem?
    -   Are edge cases handled (nulls, empty lists, network failures)?
    -   Is the logic sound and bug-free?
    -   **Action:** If checking a fix, verify it doesn't break other parts (regression).

2.  **Security** (Critical - *Refer to `security-audit` for deep dives*)
    -   **Input Validation:** Are all inputs sanitized? (No raw SQL, no unsafe HTML).
    -   **Secrets:** Are API keys or credentials hardcoded? (Immediate FAIL if found).
    -   **Auth:** Is authentication/authorization bypassed or weak?

3.  **Style & Standards** (Important - *Refer to `tech_standards.md`*)
    -   **Naming:** Do variables/functions use clear, descriptive names? (e.g., `isUserLoggedIn` vs `flag`).
    -   **Typing:** Is `any` used? (Strictly forbidden). Are types explicit?
    -   **Imports:** Are path aliases (`@/`) used instead of relative paths (`../../../`)?
    -   **Structure:** Is the file too large? Should it be broken down?

4.  **Performance** (Optimization)
    -   Are there unnecessary loops or expensive computations?
    -   Is there redundant state re-rendering (frontend)?
    -   Are database queries optimized (N+1 problems)?

## Output Format
Provide feedback in this format:

### 🚨 Critical Issues (Must Fix)
*List logical bugs, security holes, or blocking standard violations.*

### ⚠️ Improvements (Should Fix)
*List style refactors, standard violations, or performance tweaks.*

### ✅ Good Practices
*Highlight clever solutions or clean implementations.*

### Recommended Action
*Summary of what to do next (e.g., "Fix critical issues before commit" or "Approved with minor nits").*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/splashcodedex) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
