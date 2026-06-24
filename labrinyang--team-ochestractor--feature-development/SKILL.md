---
name: feature-development
description: Use when building new modules or features where teammates can each own a separate piece without stepping on each other
metadata:
  author: labrinyang
---

# Feature Development: Parallel Module Pattern

## Overview

Each teammate owns an independent module or component. They build in parallel without interfering. An integrator ensures cross-module consistency.

**Core principle:** Conway's Law as a tool — structure the team to mirror the desired architecture.

**Management theory:** Conway's Law (team structure → system architecture), T-Shaped Skills (specialists who understand the whole), Belbin role coverage.

## When to Use

- New feature with 2+ independent components
- Each component can be built and tested in isolation
- Components have well-defined interfaces/contracts
- Code ownership is clear (no two people editing same file)

**Don't use when:**
- Components are tightly coupled
- Interface contracts are undefined
- Single module that can't be split

## Team Composition

```
coordinator (lead, delegate mode recommended)
├── architect × 1          (designs interfaces, reviews contracts)
├── implementer × 2-3      (one per module)
├── reviewer × 1            (code quality + spec compliance)
└── integrator × 1          (optional: cross-module consistency)
```

**Belbin coverage:**
- Thinking: architect (Plant) + reviewer (Monitor-Evaluator)
- Action: implementers (Implementer)
- People: coordinator (Coordinator) + integrator (Teamworker)

**Sizing by feature scope:**

| Feature Size | Team | Notes |
|-------------|------|-------|
| 2 modules | 3-4 | coordinator + 2 implementers + reviewer |
| 3-4 modules | 4-5 | + architect for interface design |
| 5+ modules | 5-7 | + integrator, or split into sub-teams |

## The Process

### Phase 1: Forming — Architecture & Contracts

**Before spawning implementers:**
1. Coordinator or architect defines module boundaries
2. Define interface contracts between modules (API signatures, data shapes)
3. Create task list with dependencies
4. Each module is a task assigned to one implementer

**Critical:** Interface contracts MUST be locked before implementation starts (Tuckman's Norming). Changing contracts mid-build invalidates parallel work.

**Contract template:**
```
Module A provides:
  - function doX(input: TypeA): TypeB
  - emits event "x-complete" with payload TypeC

Module B consumes:
  - calls A.doX() with [specific inputs]
  - listens for "x-complete"

Agreed data types: [shared types file]
```

### Phase 2: Storming — Plan Review

- Architect presents module split + contracts
- Reviewer checks for gaps, ambiguities
- Devil-advocate (or reviewer) challenges: "What if module A fails? What's the error contract?"
- Iterate until contracts are solid

### Phase 3: Performing — Parallel Implementation

Each implementer:
1. Works only on their assigned module
2. Follows the agreed interface contract
3. Writes tests for their module in isolation
4. Commits independently (no file conflicts)

**File ownership rule:** No two implementers touch the same file. If shared code is needed, the integrator or architect handles it.

**Coordinator in delegate mode:**
- Does NOT implement anything
- Tracks progress via task list
- Unblocks stuck implementers
- Routes cross-module questions to architect

### Phase 4: Integration

Integrator (or coordinator):
1. Verifies all modules implement their contracts
2. Writes integration tests
3. Checks cross-cutting concerns (error handling, logging, auth)
4. Resolves any interface mismatches

### Phase 5: Adjourning — Review & Reflect

- Reviewer runs full review on integrated code
- team-orchestrator:session-reflection captures learnings

## Conway's Law: Team ↔ Architecture Mapping

Structure your team to produce the architecture you want:

| Desired Architecture | Team Structure |
|---------------------|----------------|
| Microservices | 1 implementer per service |
| Monolith with modules | 1 implementer per module, shared integrator |
| Frontend + Backend | 1 FE impl + 1 BE impl (see cross-layer pattern) |
| Plugin system | 1 impl for core + 1 per plugin |

**Anti-pattern:** Splitting by file instead of by feature. This produces files that "work" in isolation but features that don't cohere.

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Start implementing before contracts locked | Norming gate: plan must be approved |
| Two implementers editing same file | Assign file ownership explicitly |
| No integration phase | Integrator role exists for this |
| Coordinator starts coding | Enable delegate mode |
| Contracts too vague | Include data types + error cases |
| No shared types file | Architect creates it before implementation |

## Integration

**Pre-requisite:** team-orchestrator:orchestrating-work routes here
**Post-requisite:** team-orchestrator:session-reflection records learnings
**Related:** superpowers:subagent-driven-development for single-session alternative

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/labrinyang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
