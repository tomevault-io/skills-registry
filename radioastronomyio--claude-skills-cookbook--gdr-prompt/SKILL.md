---
name: gdr-prompt
description: Create effective Gemini Deep Research (GDR) prompts using Negative Space Bounding (NSB) methodology. Use when user needs to write a research prompt for GDR, wants to bound an agentic research task, or mentions NSB, "deep research prompt", or research that keeps wandering off-topic. Applies the three-layer constraint architecture (Anchors → Walls → Gates) to collapse latent space before research begins. Use when this capability is needed.
metadata:
  author: radioastronomyio
---

# GDR Prompt Creation

Create focused Gemini Deep Research prompts using NSB v0.4's layered constraint architecture.

## Core Concept

GDR's iterative research can cascade into unproductive territory. NSB prevents this through three constraint layers applied in sequence:

| Layer | Function | Operates On |
|-------|----------|-------------|
| **Anchors** | Immutable facts (budget, hardware, timeline) | Context |
| **Walls** | Hard exclusions—entire domains OFF-LIMITS | Search vectors |
| **Gates** | Conditional inclusion—PERMITTED ONLY IF | Source evaluation |

**Key insight:** Walls collapse the search space *before* GDR begins iterating. Gates filter *during* synthesis. Both are needed.

## Workflow

### 1. Extract Anchors

Ask: "What are the non-negotiable facts about your situation?"

Target categories:
- **Physical**: Space constraints, hardware specs, location
- **Economic**: Budget ceiling, ongoing costs acceptable
- **Temporal**: Deadlines, timeline requirements
- **Role/Scope**: What problem this solves, what it doesn't need to do

Anchors are facts, not preferences. If it could change based on research findings, it's not an anchor.

### 2. Derive Walls

For each anchor, ask: "What entire categories does this eliminate?"

Pattern: `NO [domain] ([brief justification tied to anchor])`

Good walls:
- Eliminate domains that *always* violate anchors
- Are keyword/topic level (not source-specific)
- Include parenthetical justification to prevent drift

Bad walls:
- Exclude things that *sometimes* have value
- Are so narrow they're really gates
- Lack anchor justification

### 3. Construct Gates

For categories with mixed utility, ask: "Under what *specific, verifiable* conditions would this be valuable?"

Pattern: `[Category]: PERMITTED ONLY IF [verifiable condition]`

Good gates:
- Have objectively verifiable conditions
- Apply to categories that survived the walls
- Specify quality thresholds or relevance checks

Bad gates:
- Use subjective criteria ("high quality", "relevant")
- Duplicate what walls already exclude
- Can't be evaluated by the model

### 4. Frame Research Questions

Questions come *after* all constraints. They assume constraints are enforced.

Effective question types:
- **Champions**: "Which X best fits these constraints?"
- **Anti-portfolio**: "What commonly recommended X should be *avoided* given these constraints?"
- **Hidden costs**: "What are the non-obvious failure modes?"
- **Timing/strategy**: "When/how should I execute?"

### 5. Define Output Structure

Specify concrete deliverables:
- Short lists with rankings
- Avoid lists with justifications
- Checklists for verification
- Price/threshold anchors

## Quick Reference

```markdown
# Deep Research: [Topic]

## I. ANCHORS (Immutable Context)
[Category] Reality:
- [Fact]: [Specific number/constraint]

## II. WALLS (Domain Exclusions)
- NO [domain] ([anchor justification])

## III. GATES (Conditional Inclusion)
- [Category]: PERMITTED ONLY IF [verifiable condition]

## IV. RESEARCH QUESTIONS
1. **[Type]**: [Question assuming constraints enforced]

## V. OUTPUT STRUCTURE
1. **[Deliverable]**: [Contents]
```

## References

For full methodology including worked examples and design principles, see [references/nsb-methodology-v0.4.md](references/nsb-methodology-v0.4.md).

For a blank template, see [assets/template.md](assets/template.md).

## Common Failure Modes

| Problem | Symptom | Fix |
|---------|---------|-----|
| Walls too broad | Valuable sources excluded | Convert to gate with conditions |
| Walls too narrow | Research still wanders | Elevate to domain-level exclusion |
| Gates unverifiable | Model ignores them | Add specific, objective criteria |
| Missing anchors | Walls feel arbitrary | Ask "why is this excluded?" → find anchor |
| Questions before constraints | Constraints ignored | Restructure prompt order |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/radioastronomyio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
