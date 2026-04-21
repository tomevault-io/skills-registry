---
name: brainstorming
description: Use when planning new features, exploring design decisions, or thinking through an approach before implementing. Helps evaluate trade-offs for architecture decisions in the ICL project.
metadata:
  author: icl-system
---

# Brainstorming

## When to Use

- User says "let's think about...", "how should we...", "what's the best way to..."
- Before starting a new phase from ROADMAP.md
- Designing a new component or feature
- Choosing between multiple implementation approaches
- Architecture decisions that affect multiple components

## Context

ICL is a standards-track project. Design decisions are hard to reverse once code ships and bindings exist. Think before building.

Reference files:
- `ICL-Docs/PLAN.md` — existing architecture decisions
- `ICL-Runtime/ROADMAP.md` — what's committed vs planned
- `CORE-SPECIFICATION.md` — constraints on all design

## Procedure

1. **Define the problem** — What exactly are we deciding? Write it as a single question.
2. **List constraints** — What does the spec require? What does ICL-Docs/PLAN.md commit to?
3. **Generate options** — At least 2-3 approaches. Don't anchor on the first idea.
4. **Evaluate trade-offs** — For each option:
   - Does it preserve determinism?
   - Does it fit the existing architecture?
   - Does it affect all bindings or just one?
   - How complex? How maintainable?
   - Does it align with standardization goals?
5. **Recommend** — Pick one and explain why.
6. **Get user confirmation** — Present the recommendation before implementing.

## Rules

- **Spec is a hard constraint** — no option that violates core spec
- **Determinism is a hard constraint** — no option that introduces non-determinism
- **ICL-Docs/PLAN.md decisions are soft constraints** — they can be revisited but not silently ignored
- **Don't over-design** — solve today's problem, not hypothetical future problems
- **Document the decision** — if it's significant, note it in ICL-Docs/PLAN.md or a comment

## Decision Template

```markdown
### Decision: [Title]

**Question:** [What are we deciding?]

**Constraints:**
- [Constraint 1]
- [Constraint 2]

**Options:**
1. **[Option A]** — [Brief description]
   - Pro: ...
   - Con: ...
2. **[Option B]** — [Brief description]
   - Pro: ...
   - Con: ...

**Recommendation:** Option [X] because [reason].
```

## Anti-Patterns

- Implementing before thinking
- Considering only one option
- Ignoring spec constraints in design
- Over-engineering for hypothetical future needs
- Not documenting significant decisions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/icl-system) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
