---
name: architecture
description: Design technical architecture from a PRD. Use when user wants to create an architecture document, define system design, plan technical implementation, or needs to translate product requirements into buildable technical specs. Reads from docs/PRD.md and outputs docs/ARCHITECTURE.md. Use when this capability is needed.
metadata:
  author: porkbutts
---

# Architecture

Transform a PRD into an implementation-ready architecture document. Read `docs/PRD.md`, analyze requirements, and produce `docs/ARCHITECTURE.md` with enough technical clarity to start coding.

## Workflow

```
START
  │
  ▼
┌─────────────────────────┐
│ Read docs/PRD.md        │ ◄── Load the PRD into context
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│ Analyze & Clarify       │ ◄── Ask questions if PRD has gaps
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│ Confirm with user       │ ◄── Summarize approach, get approval
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│ Generate Architecture   │ ◄── Write to docs/ARCHITECTURE.md
└─────────────────────────┘
```

## Step 1: Read the PRD

Read `docs/PRD.md` to understand:
- What the product does and who uses it
- User scenarios and flows
- Tech stack specified (or constraints)
- Scope boundaries (MVP vs future)

If `docs/PRD.md` doesn't exist, ask the user to provide requirements or suggest using the product-design skill first.

## Step 2: Analyze & Clarify

Identify gaps that would block architecture decisions:
- Ambiguous scaling requirements?
- Missing auth/authorization details?
- Unclear data relationships?
- Integration specifics missing?

Ask targeted questions. Don't over-ask—only what's needed to make architecture decisions.

## Step 3: Confirm with User

Before generating, summarize:
- The high-level approach (e.g., "monolith with clear module boundaries" vs "microservices")
- Key technical choices and why
- Any tradeoffs being made

Ask if they're ready to proceed or want to adjust the approach.

## Step 4: Generate Architecture Document

Write to `docs/ARCHITECTURE.md` (create directory if needed) using the template below.

## Architecture Document Template

```markdown
# [Product Name] - Architecture

## Overview

[2-3 sentences: architectural style, key patterns, deployment model]

## High-Level Architecture

[ASCII diagram showing major components and relationships]

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Client    │────▶│   Server    │────▶│  Database   │
└─────────────┘     └─────────────┘     └─────────────┘
```

### Components
| Component | Responsibility | Technology |
|-----------|---------------|------------|
| [Name] | [What it does] | [Stack] |

## Tech Stack Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Frontend | [e.g., Next.js] | [Why this fits the requirements] |
| Backend | [e.g., Node + Express] | [Why] |
| Database | [e.g., PostgreSQL] | [Why] |
| Auth | [e.g., Supabase Auth] | [Why] |
| Hosting | [e.g., Vercel + Railway] | [Why] |

## Directory Structure

```
project/
├── src/
│   ├── app/              # [Purpose]
│   ├── components/       # [Purpose]
│   ├── lib/              # [Purpose]
│   └── ...
├── public/
├── tests/
└── ...
```

### Key Directories Explained
- `src/app/` - [What goes here and why]
- `src/lib/` - [What goes here and why]

## Key Abstractions

### [Abstraction 1 Name]
**Purpose:** [What problem it solves]
**Interface:** [Key methods/properties]
**Used by:** [Which parts of the system]

## Data Flow

[Describe data flow for key scenarios]

1. **[Scenario]**: [Step-by-step flow]

## What NOT to Over-Engineer

Keep these simple for MVP:

| Area | Keep Simple | Avoid |
|------|-------------|-------|
| Auth | Use built-in auth provider | Custom JWT infrastructure |
| State | React state/context | Redux unless proven necessary |
| API | Simple REST endpoints | GraphQL unless you need it |
| Database | Single database | Read replicas, sharding |
| Caching | None initially | Redis until you measure need |
| Validation | Validate at boundaries | Defensive checks everywhere |

## Future Considerations

[Things deliberately deferred that may influence future architecture]

- [Future need] → [How current architecture accommodates it]
```

## Guidelines

**Keep it buildable**: Every section should reduce ambiguity. A developer reading this should know where to start.

**Match complexity to scope**: MVP architecture should be simple. Don't design for scale you don't have.

**Be specific**: "Use React" is less useful than "Next.js App Router with server components for data fetching."

**Justify decisions**: Every tech choice needs a reason tied to requirements, not preference.

**Name anti-patterns**: Explicitly calling out what NOT to build prevents scope creep.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/porkbutts) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
