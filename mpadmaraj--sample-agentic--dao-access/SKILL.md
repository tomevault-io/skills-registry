---
name: dao-access
description: > Use when this capability is needed.
metadata:
  author: mpadmaraj
---

# Skill: DAO Access

## Purpose
Access data sources without leaking persistence concerns
into higher layers.

---

## When to Use
- Reading or writing persistent data
- Interacting with external data sources

---

## Inputs
- Query criteria
- Persistence constraints

---

## Outputs
- DAO or repository methods
- Clear data access boundaries

---

## Governing Rules
DAO logic must not contain business rules and must follow
architecture constraints in `agents.md`.

---

## Procedure
1. Define required data operation
2. Implement minimal persistence logic
3. Keep APIs simple and explicit
4. Avoid business decisions in DAO

---

## Failure Handling
- Fail on data inconsistency
- Surface data access errors clearly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mpadmaraj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
