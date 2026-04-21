---
name: agent-native-suite
description: Design, audit, and review agent-native systems to ensure action parity and robust agent workflows. Use when this capability is needed.
metadata:
  author: abpaul
---

# Agent Native Suite

Use this skill when a product needs agent-action parity validation or agent-first workflow design.

## Coverage

- agent-native architecture design
- parity audits
- implementation reviews for agent-accessibility

## Required Inputs

- target user journeys and critical tasks
- current tool/API surface available to agents
- trust boundaries, auth rules, and error-handling constraints

## Workflow

1. Identify user actions and equivalent agent actions.
2. Verify tool/API coverage for all critical user actions.
3. Validate permission model, idempotence, and rollback behavior for agent actions.
4. Flag parity gaps and propose concrete remediation.

## Rails Touchpoints

For Rails-first systems, explicitly map:

- resourceful routes and controller actions exposed to agents
- model/service/job boundaries for write operations
- authorization and strong-params boundaries on every mutating action
- Turbo/Hotwire flows where agent actions should mirror user-visible state transitions

## Context Discipline

- Start with affected flows and touched endpoints; avoid broad architecture deep-dives unless gaps are found.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abpaul) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
