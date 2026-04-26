---
name: tdd-cycle-manager
description: Guides established workflows through the Red-Green-Refactor cycle of Test-Driven Development. Use when this capability is needed.
metadata:
  author: jcorpac
---

# TDD Cycle Manager

This skill helps maintain the discipline of the TDD cycle.

## The Cycle

### 1. 🔴 RED (Write a failing test)
- Focus on the *behavior* you want to implement.
- Write a minimal test that fails (compile error or assertion failure).
- **Goal**: Confirm the test fails for the right reason.

### 2. 🟢 GREEN (Make it pass)
- Write the *minimal* amount of code to make the test pass.
- Don't worry about aesthetics or performance yet.
- **Goal**: Get to a passing state as quickly as possible.

### 3. 🔵 REFACTOR (Clean up)
- Improve the code without changing its behavior.
- Use the tests as a safety net.
- **Goal**: Enhance readability, remove duplication, and optimize.

## Usage
Use the provided `scripts/check_phase.py` to analyze your current test results and determine your next step.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
