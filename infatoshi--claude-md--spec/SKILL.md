---
name: spec
description: Interview-driven specification development. Use when starting a new project, after context compaction, when SPEC.md is missing or stale, or when the user needs to clarify project architecture. Triggers on "spec", "interview me", "what are we building", or when substantial work is requested without clear requirements. Use when this capability is needed.
metadata:
  author: infatoshi
---

# Specification Interview

## Purpose
Prevent wrong assumptions and context loss across compactions by building a comprehensive, persistent specification through structured interviewing.

## When to Use
- Starting a new project (no SPEC.md exists)
- After compaction when resuming substantial work
- User explicitly invokes /spec
- Requirements are ambiguous and substantial work is requested
- User says "interview me" or asks to clarify architecture

## Process

### 1. Read Existing State
If SPEC.md exists, read it first to understand current state. If project CLAUDE.md exists, read that too.

### 2. Interview Protocol
Use AskUserQuestion tool to ask detailed, non-obvious questions about:

**Architecture & Design**
- Core data structures and their representations (vectors, tensors, matrices - ask for concrete shapes)
- Performance constraints and optimization parameters
- What tradeoffs are acceptable vs. non-negotiable
- System boundaries and interfaces

**Scope & Requirements**
- What is explicitly OUT of scope (as important as what's in)
- What does "done" look like - concrete success criteria
- Edge cases that matter vs. ones to ignore
- Dependencies on external systems or libraries

**Implementation Strategy**
- Preferred patterns or anti-patterns for this codebase
- What existing code/approaches to preserve vs. rewrite
- Scaling considerations (batch sizes, parallelism, memory)
- Testing strategy and validation approach

**Unknowns & Risks**
- What needs to be measured/benchmarked rather than assumed
- Technical risks or uncertainties
- What would cause this project to fail

### 3. Question Style
- Ask non-obvious questions - skip anything derivable from code
- Use concrete examples: "If batch_size=1024, does each element represent X or Y?"
- Challenge assumptions: "You said X, but that conflicts with Y - which takes priority?"
- Offer adversarial interpretations to surface hidden requirements
- Ask about visual/spatial representations when relevant (ASCII diagrams welcome)

### 4. Iterative Refinement
- Continue interviewing until the user signals completion
- After each round, summarize understanding and ask for corrections
- Identify remaining ambiguities explicitly

### 5. Write Specification
Write to ./SPEC.md at project root with structure:

```markdown
# Project: [Name]

## Objective
[1-2 sentence core goal]

## Success Criteria
- [ ] Concrete measurable outcome 1
- [ ] Concrete measurable outcome 2

## Architecture

### Core Data Structures
[Concrete representations with example shapes/values]

### System Boundaries
[What's in scope, what interfaces with external systems]

## Constraints & Tradeoffs

### Non-negotiable
- [Hard requirements]

### Acceptable Tradeoffs
- [What can be sacrificed for what]

### Out of Scope
- [Explicitly excluded]

## Implementation Strategy

### Optimization Parameters
[Batch sizes, memory limits, performance targets - MEASURED not guessed]

### Preferred Patterns
[How to structure code in this project]

### Anti-patterns
[What to avoid]

## Open Questions
[Things that still need measurement or decision]

## Reference Examples
[ASCII diagrams, example tensors, concrete values]
```

## Rules
- Never guess numerical values - mark as "TBD: needs benchmarking"
- Spec can be large (1000+ lines) - comprehensiveness beats brevity
- Update spec incrementally as project evolves
- After writing spec, remind user to review and correct

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/infatoshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
