---
name: google-design-md
description: Generate and maintain DESIGN.md files for software projects following Google Labs conventions. Use when documenting system architecture and design decisions. Use when this capability is needed.
metadata:
  author: allanninal
---

# DESIGN.md Documentation

## When to Use This Skill

- Documenting system architecture
- Recording design decisions
- Creating technical specifications
- Onboarding new team members
- Reviewing system design

## DESIGN.md Purpose

A DESIGN.md file provides high-level documentation of a software system's architecture, key design decisions, and technical approach. It serves as the authoritative source for understanding how a system works and why it was built that way.

## Standard Template

```markdown
# Design: [Project/Feature Name]

## Overview

Brief description of what this system/feature does and its primary purpose.

## Goals and Non-Goals

### Goals
- [Primary objective 1]
- [Primary objective 2]
- [Primary objective 3]

### Non-Goals
- [Explicitly out of scope item 1]
- [Explicitly out of scope item 2]

## Architecture

### System Diagram

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Client    │────▶│   API       │────▶│  Database   │
│   (React)   │     │  (Node.js)  │     │  (Postgres) │
└─────────────┘     └─────────────┘     └─────────────┘
                           │
                           ▼
                    ┌─────────────┐
                    │   Cache     │
                    │   (Redis)   │
                    └─────────────┘
```

### Components

#### [Component A]
- **Purpose**: What it does
- **Technology**: Stack used
- **Interfaces**: How it communicates

#### [Component B]
- **Purpose**: What it does
- **Technology**: Stack used
- **Interfaces**: How it communicates

## Data Model

### Entities

```
User
├── id: UUID
├── email: string
├── name: string
├── role: enum(admin, user)
└── created_at: timestamp

Post
├── id: UUID
├── user_id: UUID (FK → User)
├── title: string
├── content: text
└── published_at: timestamp
```

### Relationships
- User → Post: One-to-many

## API Design

### Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | /api/users | List users |
| GET | /api/users/:id | Get user |
| POST | /api/users | Create user |
| PUT | /api/users/:id | Update user |
| DELETE | /api/users/:id | Delete user |

### Authentication
[Describe auth approach]

### Rate Limiting
[Describe rate limiting strategy]

## Key Design Decisions

### Decision 1: [Title]

**Context**: [What problem/situation prompted this decision]

**Options Considered**:
1. [Option A] - [Brief description]
2. [Option B] - [Brief description]
3. [Option C] - [Brief description]

**Decision**: [Which option was chosen]

**Rationale**: [Why this option was selected]

**Consequences**: [Trade-offs and implications]

### Decision 2: [Title]
[Same structure]

## Security Considerations

- [Security measure 1]
- [Security measure 2]
- [Security measure 3]

## Performance Considerations

- [Performance strategy 1]
- [Performance strategy 2]
- [Performance strategy 3]

## Testing Strategy

### Unit Tests
[Approach]

### Integration Tests
[Approach]

### E2E Tests
[Approach]

## Deployment

### Infrastructure
[Description of deployment infrastructure]

### CI/CD Pipeline
[Description of pipeline stages]

### Monitoring
[Description of monitoring approach]

## Future Considerations

- [Potential future enhancement 1]
- [Potential future enhancement 2]
- [Known technical debt]

## References

- [Link to related documentation]
- [Link to external resources]
- [Link to related designs]
```

## ASCII Diagrams

### System Flow

```
┌──────────────────────────────────────────────────────────────┐
│                        SYSTEM OVERVIEW                        │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐  │
│  │ Browser │───▶│   CDN   │───▶│  Load   │───▶│   App   │  │
│  │         │    │         │    │Balancer │    │ Server  │  │
│  └─────────┘    └─────────┘    └─────────┘    └────┬────┘  │
│                                                     │        │
│                                              ┌──────┴──────┐ │
│                                              ▼             ▼ │
│                                         ┌─────────┐  ┌──────┐│
│                                         │   DB    │  │Cache ││
│                                         │(Primary)│  │      ││
│                                         └────┬────┘  └──────┘│
│                                              │               │
│                                              ▼               │
│                                         ┌─────────┐          │
│                                         │   DB    │          │
│                                         │(Replica)│          │
│                                         └─────────┘          │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### Sequence Diagram

```
Client          API           Database        Cache
   │              │               │              │
   │─── Request ──▶               │              │
   │              │── Check Cache ────────────▶ │
   │              │               │              │
   │              │◀─── Miss ─────────────────── │
   │              │               │              │
   │              │── Query ─────▶│              │
   │              │               │              │
   │              │◀─ Results ────│              │
   │              │               │              │
   │              │── Update Cache ─────────────▶│
   │              │               │              │
   │◀── Response ─│               │              │
   │              │               │              │
```

### State Machine

```
                    ┌─────────┐
        ┌──────────│  DRAFT  │◀─────────┐
        │          └────┬────┘          │
        │               │ submit        │ reject
        │               ▼               │
        │          ┌─────────┐          │
        │          │ PENDING │──────────┤
        │          │ REVIEW  │          │
        │          └────┬────┘          │
        │               │ approve       │
        │               ▼               │
        │          ┌─────────┐          │
        └──────────│PUBLISHED│          │
         unpublish └─────────┘          │
                        │               │
                        │ archive       │
                        ▼               │
                   ┌─────────┐          │
                   │ARCHIVED │──────────┘
                   └─────────┘  restore
```

## Decision Record Format (ADR)

```markdown
# ADR-001: [Title]

## Status
[Proposed | Accepted | Deprecated | Superseded by ADR-XXX]

## Context
[Describe the issue motivating this decision]

## Decision
[Describe the change we're making]

## Consequences

### Positive
- [Benefit 1]
- [Benefit 2]

### Negative
- [Drawback 1]
- [Drawback 2]

### Neutral
- [Trade-off 1]
```

## Best Practices

### Keep Updated
- Update when design changes
- Review during major refactors
- Mark outdated sections

### Be Concise
- Focus on "why" not "what"
- Code shows implementation
- Design shows reasoning

### Include Diagrams
- Visualize complex flows
- Use ASCII for portability
- Keep diagrams simple

### Document Trade-offs
- Explain alternatives considered
- State explicit non-goals
- Acknowledge technical debt

## Checklist

- [ ] Clear overview of purpose
- [ ] Goals and non-goals defined
- [ ] Architecture diagram included
- [ ] Key components documented
- [ ] Data model described
- [ ] API endpoints listed
- [ ] Design decisions recorded
- [ ] Security considerations noted
- [ ] Deployment process documented
- [ ] Future work identified

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
