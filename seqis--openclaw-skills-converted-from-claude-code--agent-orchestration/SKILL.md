---
name: agent-orchestration
description: Coordination framework for parallel specialist execution and integration consistency. Use when this capability is needed.
metadata:
  author: seqis
---

# Agent Orchestration Skill

## Overview

A coordination framework ensuring parallel agents work cohesively under master Claude orchestration. This skill is **domain-agnostic** - it provides the methodology for coordinating ANY multi-agent work, regardless of the domain.

**Cardinal Rule:** YOU (master Claude) are the ORCHESTRATOR, not a task dispatcher. Agents are specialists with bounded autonomy, not independent actors.

**For domain-specific standards, also invoke:**
- `web-frontend-standards` - Dark mode, design tokens, PWA, accessibility
- `backend-systems-protocols` - Database, API contracts, auth, error handling

## Type

workflow / coordination

## When to Use

**Trigger this skill when:**
- Launching 2+ parallel agents for any task
- Any work where agent outputs must integrate seamlessly
- Multi-file changes that must remain consistent
- Complex tasks being split across multiple agents

**Keywords:** parallel, agents, coordinate, orchestrate, multi-agent, dispatch, integration, consistency

## The Problem: Siloed Parallel Work

When agents run in parallel without coordination:
- Each makes independent decisions
- Naming conventions diverge
- Patterns conflict
- Approaches fragment
- Integration requires extensive rework

**Siloed agents = fragmented output. This is a failure mode.**

## The Solution: Master Coordinator Pattern

```
┌─────────────────────────────────────────────────────────────┐
│                   MASTER CLAUDE                              │
│            (You - the orchestrator)                          │
│                                                              │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │ PRE-FLIGHT  │→ │  DISPATCH   │→ │ POST-FLIGHT │          │
│  │  - Context  │  │  - Bounded  │  │  - Review   │          │
│  │  - Manifest │  │  - Aligned  │  │  - Unify    │          │
│  │  - Contracts│  │  - Tasked   │  │  - Integrate│          │
│  └─────────────┘  └─────────────┘  └─────────────┘          │
└─────────────────────────────────────────────────────────────┘
                           │
           ┌───────────────┼───────────────┐
           ▼               ▼               ▼
    ┌──────────┐    ┌──────────┐    ┌──────────┐
    │ Agent A  │    │ Agent B  │    │ Agent C  │
    │ Bounded  │    │ Bounded  │    │ Bounded  │
    │ Context  │    │ Context  │    │ Context  │
    └──────────┘    └──────────┘    └──────────┘
```

## Phase 1: Pre-Flight (NEVER SKIP)

**Purpose:** Establish shared reality before ANY parallel dispatch

### Pre-Flight Checklist

- [ ] Analyzed FULL scope of work
- [ ] Identified ALL interdependencies between tasks
- [ ] Defined shared conventions (naming, patterns, structure)
- [ ] Listed constraints agents MUST follow
- [ ] Listed anti-patterns agents MUST avoid
- [ ] Created coordination manifest
- [ ] Invoked domain-specific skills as needed

### Coordination Manifest Template

Every agent receives this context:

```markdown
## Coordination Manifest

### Project Context
[Brief description of what we're building]

### Shared Constraints (MUST FOLLOW)
- Naming: [convention]
- File structure: [where things go]
- Patterns: [required patterns]
- Anti-patterns: [forbidden approaches]

### Domain Standards
[Reference domain skills: web-frontend-standards, backend-systems-protocols, etc.]

### Your Bounded Task
[Specific assignment with clear boundaries]

### Integration Points
[How your work connects to other agents' work]

### Escalation Triggers
[When to stop and report back vs. make a decision]
```

## Phase 2: Dispatch (Bounded Autonomy)

**Purpose:** Launch agents with clear boundaries and shared context

### Dispatch Rules

1. **Every agent gets the coordination manifest**
2. **Specify explicit boundaries** - what they own, what they don't
3. **Define integration interfaces** - how their work connects to others
4. **Set escalation triggers** - when to stop and ask vs. proceed

### Agent Prompt Template

```markdown
You are working as part of a coordinated multi-agent team.

## Coordination Manifest
[Paste manifest here]

## Your Specific Task
[Bounded, specific assignment]

## Your Boundaries
- You OWN: [specific files/components/functions]
- You do NOT touch: [other agents' territory]
- Interface with: [how your work connects]

## Constraints (Non-Negotiable)
[List from manifest - agents MUST follow these]

## Escalation
If you encounter:
- Decisions affecting other agents' work → STOP, report back
- Conflicts with constraints → STOP, report back
- Uncertainty about scope → STOP, report back
```

## Phase 3: Post-Flight (Integration)

**Purpose:** Unify agent outputs into coherent whole

### Post-Flight Checklist

- [ ] Reviewed ALL agent outputs
- [ ] Checked for naming conflicts
- [ ] Verified pattern consistency
- [ ] Identified duplicated code/logic
- [ ] Resolved style/approach divergence
- [ ] Unified shared utilities
- [ ] Integration tested complete system
- [ ] ONLY THEN: mark task complete

### Common Integration Issues

| Issue | Detection | Resolution |
|-------|-----------|------------|
| Naming conflicts | Grep for duplicates | Standardize, update all refs |
| Pattern divergence | Code review | Pick best, refactor others |
| Duplicated logic | Diff analysis | Extract to shared utility |
| Interface mismatches | Integration test | Align contracts |
| Style inconsistency | Review | Apply shared standards |

## Domain Skill Integration

When the task involves specific domains, invoke the relevant skills BEFORE pre-flight:

| Domain | Skill to Invoke | What It Provides |
|--------|-----------------|------------------|
| Web UI | `web-frontend-standards` | Design tokens, dark mode, accessibility |
| Backend | `backend-systems-protocols` | Database, API contracts, auth |
| Debugging | `systematic-debugging` | Root cause analysis |
| Architecture | `architecture-patterns` | Design decisions |
| Security | `security-best-practices` | Auth, OWASP, encryption |

**Pattern:**
1. Invoke domain skills to get standards
2. Include those standards in coordination manifest
3. Dispatch agents with manifest
4. Post-flight includes domain-specific verification

## Anti-Patterns (What NOT to Do)

**NEVER do these:**

1. **Dispatch without manifest** - Agents will make conflicting decisions
2. **Skip pre-flight** - "They'll figure it out" = guaranteed rework
3. **Vague boundaries** - "Work on the UI" → overlapping/conflicting work
4. **No post-flight review** - Miss integration issues until user finds them
5. **Assume consistency** - Always verify, never assume
6. **Let agents pick patterns** - YOU decide, agents implement
7. **Skip domain skills** - Generic coordination without domain standards = inconsistency

**Red flags you're about to violate this skill:**
- "Just split it up and run parallel"
- "They can coordinate themselves"
- "Ship it, we'll fix inconsistencies later"
- "Each agent can choose their approach"

## Quick Reference

### Minimal Pre-Flight (Small Tasks)

Even for quick parallel work:
1. Define naming convention
2. Specify file ownership
3. State integration point
4. Dispatch

### Full Pre-Flight (Complex Tasks)

1. Invoke domain skills
2. Create detailed manifest
3. Define all constraints
4. Document integration points
5. Set escalation triggers
6. Dispatch with full context

### Post-Flight Verification

1. Review all outputs
2. Check naming consistency
3. Verify patterns match
4. Integration test
5. Mark complete

## Real-World Impact

**Without coordination:**
- 3 agents build 3 different patterns
- Contracts don't match between components
- Approaches conflict
- Integration takes longer than the parallel work saved

**With coordination:**
- Unified patterns from first agent dispatch
- Contracts defined before implementation
- Single approach throughout
- Integration is merge, not rework

**The math:**
- Skip coordination: save 10 min, waste 2 hours on integration
- Follow process: invest 15 min, seamless integration

---

*Domain-agnostic coordination methodology*
*Combine with domain skills for complete guidance*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seqis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
