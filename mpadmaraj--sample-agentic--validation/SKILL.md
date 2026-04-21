---
name: validation
description: > Use when this capability is needed.
metadata:
  author: mpadmaraj
---

# Skill: Input Validation

## Purpose
Ensure all external inputs are validated before processing.

---

## When to Use
- Handling user or external input
- Accepting request parameters or payloads

---

## Inputs
- Input fields
- Validation rules
- Constraints and limits

---

## Outputs
- Validation logic
- Clear validation failure responses

---

## Governing Rules
Validation must align with Security and Error Handling
guidelines in `agents.md`.

---

## Procedure
1. Identify all external inputs
2. Define validation rules for each input
3. Fail fast on invalid data
4. Provide clear error messages

---

## Failure Handling
- Stop execution on validation failure
- Do not proceed to business logic

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mpadmaraj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
