---
name: skill-hook-mechanisms
description: Hook mechanisms for skill discovery. Keywords: hooks, skills, mechanisms. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Hook Mechanisms (Skill Consumption)

This document describes how lifecycle hooks can use the skill rules configuration.

---

## 1. Lifecycle events (where skills can be applied)

The template defines these key events:
- `PromptSubmit` (blocking, AI-visible)
- `PreAbilityCreate` (blocking, AI-visible)
- `PreAbilityCall` (blocking, AI-visible)
- `PostAbilityCall` (non-blocking, infra-only)
- `SessionStop` (non-blocking, infra-only)

See `/.system/skills/ssot/repo/architecture-core-mechanisms/runtime-model/SKILL.md` for details.

---

## 2. Suggested usage patterns

### PromptSubmit (suggest)

Goal: suggest relevant skills based on prompt keywords/intent patterns.

Inputs:
- user prompt text
- (optional) current working scope inferred from routing

Output:
- a short list of recommended skill entrypoints under `/.system/skills/ssot/**`

### PreAbilityCreate / PreAbilityCall (guard)

Goal: enforce guardrails when executing risky abilities.

Inputs:
- ability id / operation key
- target files/paths (if known)

Output:
- allow/deny/requires_human (policy decision), plus recommended skills to consult

---

## 3. Safety notes

- Avoid over-blocking: prefer `suggest` unless the risk is truly stop-the-line.
- Any enforcement change that can block execution should require explicit human approval.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
