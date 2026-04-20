---
name: brainstorming
description: Conducts structured design brainstorming with clarifying questions and multi-perspective exploration. Use when adding features, building something new, implementing integrations, or when requirements are vague. Triggers on "add", "build", "implement", "create", "design", or open-ended requests. Use when this capability is needed.
metadata:
  author: nxtaar
---

# Brainstorming

Structured Q&A sessions with parallel perspective exploration to clarify design goals before implementation.

## When to Activate

- User mentions adding a new feature or integration
- User asks to "build", "implement", "create", or "add" something
- Requirements are vague or open-ended
- Multiple implementation approaches are possible
- Starting a new project or major feature

## Process

### 1. Understand the Request

Parse the user's request to identify:
- **Core objective** - What they want to achieve
- **Constraints** - Time, tech stack, compatibility requirements
- **Context** - Existing codebase, dependencies, team preferences

### 2. Parallel Perspective Exploration

Spawn 2-3 agents to explore different angles simultaneously:

**Technical Feasibility Agent:**
- Analyze implementation complexity
- Identify technical constraints
- Evaluate tech stack fit
- Assess integration requirements

**User Experience Agent:**
- Explore user workflows
- Identify pain points addressed
- Consider edge cases in usage
- Map user journey

**Risk Assessment Agent:**
- Identify potential blockers
- Evaluate dependencies
- Assess scope creep risk
- Note security considerations

### 3. Explore the Codebase

Before asking questions, gather context:
- Search for related existing code
- Identify patterns and conventions used
- Note dependencies and integrations
- Find similar implementations for reference

### 4. Ask Clarifying Questions

Use AskUserQuestion tool for structured input. Focus on:

**Scope Questions:**
- What is the minimum viable version?
- What's must-have vs nice-to-have?
- What's explicitly out of scope?

**Technical Questions:**
- Preferred technologies or patterns?
- Integration points with existing systems?
- Performance or scalability requirements?
- Security/compliance requirements?

**User Experience Questions:**
- Who are the primary users?
- What workflows should this support?
- How should errors be handled?

### 5. Synthesize Design Direction

After gathering answers:
1. Summarize understood requirements
2. Aggregate insights from parallel explorations
3. Identify conflicts requiring resolution
4. Propose approach options with trade-offs
5. Recommend path forward with rationale

## Human-in-the-Loop Rules

- If acceptance criteria are incomplete → Ask numbered questions and WAIT
- If conflicting requirements detected → Present trade-offs and WAIT
- If scope is ambiguous → Clarify boundaries before proceeding
- Never assume business decisions → Always confirm with user

## Output Format

```markdown
## Design Summary: [Feature Name]

### Objectives
- **Primary goal:** [Clear outcome]
- **Success criteria:** [Measurable indicators]
- **Out of scope:** [Explicit exclusions]

### Requirements Gathered
| Category | Requirement | Priority | Notes |
|----------|-------------|----------|-------|
| [Area] | [Req] | Must/Should/Could | [Context] |

### Technical Approach
- **Architecture:** [High-level design]
- **Key components:** [Major pieces]
- **Integration points:** [External systems]
- **Patterns to follow:** [Existing conventions]

### Trade-offs Considered
| Option | Pros | Cons | Recommendation |
|--------|------|------|----------------|
| [A] | [Benefits] | [Drawbacks] | [Yes/No] |

### Open Questions
- [Any remaining uncertainties]

### Risks Identified
- [Technical risks]
- [Scope risks]
- [Dependency risks]

### Recommended Next Step
[Clear action - typically invoke planning skill]
```

## Agent Dispatch Templates

### Technical Feasibility Agent
```
Analyze technical feasibility for:

## Feature
[Description]

## Current Stack
[Technologies in use]

## Tasks
1. Assess implementation complexity (S/M/L)
2. Identify required changes to existing code
3. Note integration challenges
4. Recommend approach

Be specific about technical trade-offs.
```

### Risk Assessment Agent
```
Assess risks for proposed feature:

## Feature
[Description]

## Context
[Project state, timeline, dependencies]

## Tasks
1. Identify technical risks
2. Note scope creep potential
3. Assess dependency risks
4. Flag security considerations

Rank risks by impact and likelihood.
```

## Collaboration

After brainstorming is complete, suggest invoking the **planning** skill to create a structured implementation plan with tasks and verification steps.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nxtaar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
