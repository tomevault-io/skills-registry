---
name: test-generation
description: > Use when this capability is needed.
metadata:
  author: mpadmaraj
---

# Skill: Test Generation

## Purpose
Create meaningful tests that verify behavior, not implementation.

---

## When to Use
- Adding new service logic
- Modifying existing business behavior

---

## Inputs
- Service methods
- Expected behaviors
- Edge cases

---

## Outputs
- Unit tests
- Clear assertions
- Readable test names

---

## Governing Rules
Tests must follow Testing guidelines in `agents.md`.

---

## Procedure
1. Identify behaviors to test
2. Write tests for happy and failure paths
3. Keep tests readable and focused
4. Avoid excessive mocking

---

## Failure Handling
- Stop if behavior is unclear
- Flag untestable logic

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mpadmaraj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
