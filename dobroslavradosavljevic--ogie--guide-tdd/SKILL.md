---
name: guide-tdd
description: Triggered when user asks to follow TDD, enforce TDD practices, or guide TDD workflow. Automatically delegates to the tdd-guide agent. Use when this capability is needed.
metadata:
  author: dobroslavradosavljevic
---

# Guide TDD Skill

## Trigger Phrases

This skill is automatically triggered when the user:

- Asks to "use TDD" or "follow TDD"
- Requests TDD workflow or guidance
- Wants to "write tests first" or "TDD approach"
- Mentions "test-driven development" or "red-green-refactor"
- Asks about TDD methodology

## Delegation Instructions

When this skill is triggered:

1. **Delegate immediately** to the `tdd-guide` agent
2. Specify feature or code to implement with TDD
3. Include TDD requirements or constraints
4. Provide current code state
5. Include test coverage goals

## Context to Pass

- **Feature**: What to implement with TDD
- **Requirements**: Feature requirements
- **Current State**: Existing code or tests
- **Coverage Goal**: Target test coverage
- **TDD Focus**: Specific TDD aspects to emphasize
- **Constraints**: Any constraints or preferences

## Agent Responsibilities

The tdd-guide agent will:

1. Guide through Red-Green-Refactor cycle
2. Ensure tests are written first
3. Enforce TDD discipline
4. Maintain high test coverage (80%+)
5. Review test quality
6. Ensure minimal implementation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dobroslavradosavljevic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
