---
name: prompt-to-spec
description: > Use when this capability is needed.
metadata:
  author: kevcofett
---

# Prompt-to-Spec

## Commands

- `/spec <idea>` — Start the 3-phase refinement process
- `/spec refine <path>` — Refine an existing spec

## Procedure

### Phase 1: EXTRACT

Ask clarifying questions to expose hidden assumptions:

1. Who is the user/audience?
2. What problem does this solve?
3. What does success look like?
4. What are the constraints (time, tech, budget)?
5. What is explicitly out of scope?
6. Are there existing systems this must integrate with?

Do NOT proceed until all ambiguities are resolved. Ask follow-up questions if answers are vague.

### Phase 2: STRUCTURE

Generate a structured spec document:

```
# Spec: {Title}

## Problem Statement
{1-2 sentences describing the problem}

## Solution Overview
{1-2 sentences describing the approach}

## User Stories
- As a {role}, I want {capability} so that {benefit}

## Requirements

### Functional
- FR-1: {requirement}
- FR-2: {requirement}

### Non-Functional
- NFR-1: {performance, security, scalability requirement}

## Acceptance Criteria
- AC-1: Given {context}, when {action}, then {expected result}
- AC-2: Given {context}, when {action}, then {expected result}

## Out of Scope
- {explicitly excluded items}

## Dependencies
- {systems, APIs, data sources needed}

## Open Questions
- {unresolved items that need answers before implementation}
```

### Phase 3: VALIDATE

Present the spec to the user and ask:

1. Does this accurately capture what you want?
2. Are the acceptance criteria testable and complete?
3. Is anything missing from the requirements?
4. Is the scope right (not too big, not too small)?

Iterate until the user signs off, then save to `docs/specs/{slug}.md`.

## Rules

- NEVER skip Phase 1 — vague specs lead to wrong implementations
- Every requirement MUST have at least one acceptance criterion
- Acceptance criteria MUST be testable (given/when/then format)
- Out of Scope section is mandatory — prevents scope creep

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevcofett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
