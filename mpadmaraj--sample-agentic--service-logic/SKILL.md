---
name: service-logic
description: > Use when this capability is needed.
metadata:
  author: mpadmaraj
---

# Skill: Service Logic

## Purpose
Implement business rules in the service layer only.

---

## When to Use
- Adding or modifying business logic
- Coordinating multiple operations

---

## Inputs
- Business requirements
- Validated input data

---

## Outputs
- Service-layer implementation
- Business rule enforcement

---

## Governing Rules
Service logic must comply with Architecture and Coding Standards
in `agents.md`.

---

## Procedure
1. Identify business responsibility
2. Implement logic in small, readable methods
3. Avoid framework-specific code in logic
4. Prepare logic for testing

---

## Failure Handling
- Fail on unclear business rules
- Surface domain errors explicitly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mpadmaraj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
