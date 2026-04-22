---
name: property-test
description: Design and implement property-based tests that verify invariants across randomized inputs. Use when user says "property test", "invariant test", "fuzz inputs", or wants to discover edge cases that example-based tests miss. Use when this capability is needed.
metadata:
  author: gr1m0h
---

Design and implement property-based tests for the specified target.

## Context

Existing test framework:
!`ls jest.config* vitest.config* pytest.ini pyproject.toml Cargo.toml 2>/dev/null | head -5`

## Target: $ARGUMENTS

## Instructions

1. Analyze target code and identify algebraic properties (roundtrip, idempotency, invariants, etc.)
2. Design generators for input types
3. Implement property tests using the project's framework
4. Run tests and verify all properties hold
5. Report any counterexamples found

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gr1m0h) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
