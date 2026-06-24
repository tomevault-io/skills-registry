---
name: project-docs
description: Project brief and PRD patterns for non-technical project documentation. Use when running /my_project, creating project briefs, defining product requirements, or asking about value propositions and target audiences. Use when this capability is needed.
metadata:
  author: anilcancakir
---

# Project Documentation Skill

Patterns and templates for creating project briefs and product requirements documents (PRDs).

## Purpose

Create **non-technical** project documentation that answers:
- **What** is this project?
- **Who** is it for?
- **Why** does it exist?
- **What problem** does it solve?

This is NOT technical documentation. For technical docs, use CLAUDE.md.

## Project Brief vs PRD vs CLAUDE.md

| Document | Purpose | Audience |
|----------|---------|----------|
| project.md | Project identity and goals | Stakeholders, team |
| brand.md | Visual and verbal identity | Designers, marketing |
| CLAUDE.md | Technical configuration | Claude Code |

## Project.md Structure

### Minimal (MVP/Side Projects)

```markdown
# [Project Name]

> [Tagline - one memorable sentence]

[2-3 sentence description]

## Problem

[What pain point does this solve?]

## Solution

[How does your project solve it?]

## Target User

[Who is this for?]
```

### Standard (Startups/Products)

```markdown
# [Project Name]

> [Tagline]

[Description]

## Problem

**What**: [The pain point]
**Who**: [Who experiences it]
**Impact**: [Cost of the problem]

## Solution

[Value proposition]

## Target Audience

**Primary User**: [Role and context]
- Technical level: [Level]
- Key goal: [What they want]

## Core Features

| Feature | Benefit |
|---------|---------|
| [Name] | [User value] |

## Success Metrics

| Metric | Target | Timeframe |
|--------|--------|-----------|
| [KPI] | [Goal] | [When] |
```

### Comprehensive (Enterprise/Funded)

```markdown
# [Project Name]

> [Tagline]

[Description]

## Executive Summary

[One paragraph overview for executives]

## Problem Statement

**Problem**: [Detailed pain point]
**Evidence**: [Data, research, quotes]
**Who**: [Target segment with size]
**Impact**: [Business cost if unsolved]

## Solution

**Value Proposition**:
For [TARGET USER]
who [NEED/PROBLEM],
[PROJECT NAME] is a [CATEGORY]
that [KEY BENEFIT].
Unlike [ALTERNATIVES],
we [UNIQUE DIFFERENTIATOR].

## Target Audience

### Primary Persona
- **Name**: [Persona name]
- **Role**: [Job title/context]
- **Goals**: [What they want to achieve]
- **Pains**: [Current frustrations]
- **Technical**: [Skill level]

### Secondary Persona
[Similar structure]

## Core Features

### MVP Features (Must Have)
| Feature | Benefit | Priority |
|---------|---------|----------|
| [Name] | [Value] | P0 |

### Future Features (Nice to Have)
| Feature | Benefit | Priority |
|---------|---------|----------|
| [Name] | [Value] | P1/P2 |

## Success Metrics

### Leading Indicators
- [Metric]: [Target] by [Date]

### Lagging Indicators
- [Metric]: [Target] by [Date]

## Market Context

[If market research was conducted]

### Competitive Landscape
| Competitor | Strength | Weakness | Our Advantage |
|------------|----------|----------|---------------|

### Market Size
- TAM: [Total Addressable Market]
- SAM: [Serviceable Addressable Market]
- SOM: [Serviceable Obtainable Market]

## Out of Scope

[What we're explicitly NOT building]

## Risks & Mitigation

| Risk | Impact | Mitigation |
|------|--------|------------|
| [Risk] | [High/Med/Low] | [How to address] |
```

## Key Principles

### 1. Start with Why
- Lead with the problem, not the solution
- Show evidence of the pain point
- Make the reader care

### 2. User-Centric Language
- Features are benefits, not specs
- Use "you" and "your"
- Avoid technical jargon

### 3. Be Specific
- "30% time savings" not "faster"
- "Small business owners" not "users"
- Concrete examples over abstractions

### 4. Action-Oriented
- Each section should inform decisions
- Include success metrics
- Define clear scope

## Anti-Patterns to Avoid

| Avoid | Instead |
|-------|---------|
| "Built with React" | "Instant, responsive interface" |
| "Uses PostgreSQL" | "Your data is secure and backed up" |
| "RESTful API" | "Integrates with your existing tools" |
| "Users" | "Small business owners" |
| "Better" | "50% faster invoice processing" |

## Feature Writing Guidelines

### Good Feature Descriptions

```markdown
## Smart Scheduling
Never miss a deadline. Our AI analyzes your workload and suggests
the optimal time to work on each task based on your energy patterns.

**Benefit**: Spend less time planning, more time doing.
```

### Bad Feature Descriptions

```markdown
## Smart Scheduling
Uses machine learning algorithms with TensorFlow to optimize task
scheduling based on historical data analysis and user behavior patterns.

[Too technical, no clear benefit]
```

## Value Proposition Templates

### For B2B SaaS
```
For [TARGET ROLE] at [COMPANY TYPE]
who struggle with [PAIN POINT],
[PRODUCT] is a [CATEGORY]
that [PRIMARY BENEFIT].
Unlike [COMPETITOR/ALTERNATIVE],
we [UNIQUE VALUE] because [REASON].
```

### For Consumer Apps
```
[PRODUCT] helps [TARGET USER] [ACHIEVE GOAL]
by [HOW IT WORKS].
```

### For Developer Tools
```
[PRODUCT]: [WHAT IT IS] that [KEY BENEFIT].
Stop [PAIN] and start [POSITIVE OUTCOME].
```

## Integration with idea-research Plugin

If the idea-research plugin is installed, project.md can include:

- **Market Validation**: SWOT analysis, idea scoring
- **Competitive Analysis**: Competitor mapping, positioning
- **Market Sizing**: TAM/SAM/SOM calculations
- **Trend Analysis**: Timing assessment, growth drivers

Reference the idea-research agents for detailed market research.

## References

For detailed templates and examples, see:
- [references/documentation-patterns.md](references/documentation-patterns.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anilcancakir) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
