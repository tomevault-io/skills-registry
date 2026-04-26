---
name: test-driven-development
description: MANDATORY when doing TDD: make a Test List, pick one, Red→Green→Refactor, update the list, repeat. Always open references/test-driven-development.md. Use when this capability is needed.
metadata:
  author: shunta-sato
---

## Purpose

Use this skill to keep TDD as a repeatable procedure: you do not “jump ahead” into a large implementation.

The workflow is: **Test List → pick one → Red → Green → Refactor → update the list → repeat**.

## When to use

Use this skill when:

- You are adding new behavior and can drive it from tests.
- You are about to refactor and want tests to protect the behavior.
- You want to keep changes small and verifiable.

## How to use

0) Open `references/test-driven-development.md`. Start from the template.

1) Write a Test List first (3–10 items). Pick the smallest item.

2) Red: write the failing test with a clear name and intent.

3) Green: implement the simplest code to pass.

4) Refactor: improve readability / modularity while keeping tests green.

5) Repeat: update the Test List (split or reorder), then go back to step 2.

## Output expectation

- Always show the current Test List and which item you are implementing now.
- Keep each iteration small; avoid combining multiple behaviors in one step.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shunta-sato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
