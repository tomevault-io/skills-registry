---
name: refactoring-04-data-io-validation
description: Use when improving data loading, validation, and IO boundaries in Python research code.
metadata:
  author: silviase
---

# Refactoring 04: Data IO and Validation

## Goal

Make data handling reliable with clear schemas, validated inputs, and consistent IO boundaries.

## Sequence

- Order: 04
- Previous: refactoring-03-config-reproducibility
- Next: refactoring-05-testing-regression

## Workflow

- Create a single data access layer (loaders, paths, caching) used by all entrypoints.
  - Success: Entrypoints share one data loading API.
- Define expected schemas (columns, dtypes, shapes) and validate inputs early.
  - Success: Invalid inputs are rejected with clear errors.
- Add light weight checksums or version tags to datasets where practical.
  - Success: Dataset versions are recorded and comparable.
- Keep preprocessing steps deterministic and logged.
  - Success: Preprocessing outputs are repeatable and traceable.
- Separate raw, intermediate, and final outputs with clear folder names.
  - Success: Output folders are consistent and documented.

## Guardrails

- Avoid changing dataset content unless explicitly requested.
- Keep validation checks fast and focused on critical assumptions.
- Do not duplicate IO logic across scripts and notebooks.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/silviase) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
