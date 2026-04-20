---
name: architecture
description: Design and evolve project architecture with explicit boundaries, typed contracts, and low coupling. Use when defining module structure or introducing new abstractions. Use when this capability is needed.
metadata:
  author: guillaume-lombardo
---

# Architecture Skill

## Purpose

Design a maintainable architecture with clear contracts and low coupling.

## Workflow

1. Clarify objective, scope, and constraints first.
2. Define or update typed contracts first (models, protocols, interfaces).
3. Choose simple abstractions before adding polymorphism.
4. Decouple domain logic, orchestration, and infrastructure adapters.
5. Record architecture tradeoffs in `plan.md`.

## Mandatory Decisions

- Define thin interfaces where external dependencies are involved.
- Keep one canonical schema per external contract (CLI/API/config).
- Make compatibility and migration behavior explicit when contracts evolve.

## Deliverables

- Update interfaces and protocols.
- Update module boundaries.
- Update plan entries for architecture decisions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guillaume-lombardo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
