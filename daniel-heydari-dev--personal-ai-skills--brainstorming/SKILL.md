---
name: brainstorming
description: You MUST use this before any creative work - creating features, building components, adding functionality, or modifying behavior. Explores user intent, requirements and design before implementation. Use when this capability is needed.
metadata:
  author: daniel-heydari-dev
---

# Brainstorming Ideas Into Designs

## Overview

Help turn ideas into fully formed designs and specs through natural collaborative dialogue.

Start by understanding the current project context, then ask questions one at a time to refine the idea. Once you understand what you're building, present the design in small sections (200-300 words), checking after each section whether it looks right so far.

## The Process

### Understanding the Idea

1. **Check out the current project state first** (files, docs, recent commits)
2. **Ask questions one at a time** to refine the idea
3. **Prefer multiple choice questions** when possible, but open-ended is fine too
4. **Only one question per message** - if a topic needs more exploration, break it into multiple questions
5. **Focus on understanding:** purpose, constraints, success criteria

### Exploring Approaches

1. **Propose 2-3 different approaches** with trade-offs
2. **Present options conversationally** with your recommendation and reasoning
3. **Lead with your recommended option** and explain why

### Presenting the Design

1. **Once you believe you understand** what you're building, present the design
2. **Break it into sections** of 200-300 words
3. **Ask after each section** whether it looks right so far
4. **Cover:** architecture, components, data flow, error handling, testing
5. **Be ready to go back** and clarify if something doesn't make sense

## After the Design

### Documentation

- Write the validated design to `docs/plans/YYYY-MM-DD-<topic>-design.md`
- Use clear, concise writing
- Commit the design document to git

### Implementation (If Continuing)

- Ask: "Ready to set up for implementation?"
- Create isolated workspace if needed
- Create detailed implementation plan

## Key Principles

| Principle                     | Description                                           |
| ----------------------------- | ----------------------------------------------------- |
| **One question at a time**    | Don't overwhelm with multiple questions               |
| **Multiple choice preferred** | Easier to answer than open-ended when possible        |
| **YAGNI ruthlessly**          | Remove unnecessary features from all designs          |
| **Explore alternatives**      | Always propose 2-3 approaches before settling         |
| **Incremental validation**    | Present design in sections, validate each             |
| **Be flexible**               | Go back and clarify when something doesn't make sense |

## Question Templates

### Understanding Purpose

```
What problem are we trying to solve?

A) Users can't find what they're looking for
B) The current solution is too slow
C) We need to support a new use case
D) Other: [please describe]
```

### Clarifying Scope

```
Which of these should be in scope for v1?

A) Just the core feature (minimum viable)
B) Core + basic integrations
C) Full feature set with all integrations
D) Let me explain the specific scope...
```

### Technical Decisions

```
For data storage, I see a few options:

A) **PostgreSQL** - Best for relational data, complex queries
   Trade-off: More setup, need migrations

B) **MongoDB** - Best for flexible schemas, rapid iteration
   Trade-off: Less suited for complex joins

C) **Redis** - Best for caching, real-time features
   Trade-off: Data persistence requires configuration

I'd recommend **A (PostgreSQL)** because [reason].
What do you think?
```

## Design Document Template

```markdown
# Design: [Feature Name]

## Summary

One paragraph describing what we're building and why.

## Goals

- Primary goal
- Secondary goal
- Non-goals (explicitly out of scope)

## User Stories

- As a [user], I want to [action] so that [benefit]

## Proposed Solution

### Architecture

[Diagram or description of system architecture]

### Components

1. **Component A** - Description and responsibility
2. **Component B** - Description and responsibility

### Data Model

[Schema or data structure definitions]

### API Design

[Endpoint definitions if applicable]

## Alternatives Considered

### Option B: [Name]

- Pros: ...
- Cons: ...
- Why not chosen: ...

## Implementation Plan

### Phase 1: Foundation

- [ ] Task 1
- [ ] Task 2

### Phase 2: Core Feature

- [ ] Task 3
- [ ] Task 4

## Testing Strategy

- Unit tests for...
- Integration tests for...
- E2E tests for...

## Open Questions

- Question 1?
- Question 2?

## References

- [Link to related docs]
- [Link to similar implementations]
```

## Example Session

```
User: I want to add a notifications feature

AI: Let me understand what you're looking for. What type of
notifications are you thinking about?

A) In-app notifications (bell icon, notification center)
B) Email notifications
C) Push notifications (mobile/desktop)
D) All of the above
E) Something else

User: A - just in-app for now

AI: Great, keeping scope focused. For the notification types,
which are most important for v1?

A) System alerts (errors, maintenance)
B) User actions (someone commented, liked, etc.)
C) Reminders/scheduled notifications
D) All of these

User: B primarily, maybe A for critical stuff

AI: Perfect. Here's my recommended approach:

**Real-time notifications via WebSocket**
- Instant delivery when events happen
- Connection stays open for live updates
- Falls back to polling if WS fails

Trade-off: More complex than polling, but much better UX.

Alternative: Simple polling every 30s
- Simpler to implement
- But delayed notifications, more server load

I'd recommend WebSocket. Does this direction make sense?

User: Yes, websocket sounds good

AI: Great! Let me outline the architecture in sections.
I'll check in after each part.

**Section 1: Data Model**

We'll need:
- `notifications` table with: id, user_id, type, title,
  body, read_at, created_at
- `notification_preferences` for user settings

Does this data model look right before I continue?

[continues section by section...]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daniel-heydari-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
