---
name: trd
description: Create a Technical Requirements Document - define HOW to build what the PRD specified. Adaptive depth based on project complexity. Use after PRD approval. Use when this capability is needed.
metadata:
  author: solstice035
---

# Technical Requirements Document (TRD)

## Workflow Position

```
superpowers:brainstorming
        ↓
    personal:prd           ← You should have this BEFORE using this skill
        ↓
    personal:trd           ← YOU ARE HERE
        ↓                        ↓
        ↓    [Technical blocker discovered?]
        ↓    → Return to personal:prd with constraints
        ↓
    [Complex?] → mcp__pal__consensus → Design validation
        ↓
    [Touches auth/payments/PII?] → mcp__pal__secaudit
        ↓
superpowers:writing-plans  → Next: Break into executable tasks
```

**TRD → PRD Feedback Loop:** If technical analysis reveals constraints that invalidate product requirements (e.g., API rate limits breaking a user journey, infeasible architecture), STOP and return to PRD with the discovered constraints.

**This skill is Step 3 of the Planning Phase.** See [WORKFLOW.md](../../WORKFLOW.md) for the complete workflow.

---

## Overview

Transform PRD requirements into technical design decisions. This skill helps you think through the HOW - architecture, data models, and technology choices.

**Adaptive complexity:** This skill asks whether your project is simple or complex, then adjusts the depth of documentation accordingly.

---

## When to Use

- After `personal:prd` when requirements are approved
- When you need to think through technical approach before coding
- When a project is complex enough that you might forget architectural decisions

## When to Skip

- Trivial changes with obvious implementation
- Bug fixes where the solution is clear
- When the PRD is so simple that technical decisions are obvious

---

## The Process

### Step 0: Load PRD Context

First, find and read the PRD:
- Look in `docs/plans/` for the most recent `*-prd.md`
- If not found, ask: "Where is your PRD, or shall we create one first with `personal:prd`?"
- Extract user stories and acceptance criteria as requirements

### Step 1: Determine Complexity

Ask the user:

> **How complex is this project?**
>
> **Simple** - Single component, familiar patterns, no new data models, < 1 day to build
> - You'll get: Architecture overview, key decisions, risks
> - After TRD: Go directly to `superpowers:writing-plans`
>
> **Complex** - Multiple components, new data models, external integrations, > 1 day to build
> - You'll get: Full data models, API design, component interactions, detailed risks
> - After TRD: Run `mcp__pal__consensus` for design validation, then `superpowers:writing-plans`

Wait for answer before proceeding.

---

## Simple Mode

For simple projects, cover these sections:

### Section 1: Architecture Overview

Describe in 2-3 paragraphs:
- High-level approach (what components, how they connect)
- Why this approach (brief rationale)
- Key patterns being used

Present and ask: "Does this architecture make sense?"

### Section 2: Technology Choices

Create a simple table:

| Component | Choice | Why |
|-----------|--------|-----|
| Language | [choice] | [rationale] |
| Framework | [choice] | [rationale] |
| Storage | [choice] | [rationale] |
| ... | ... | ... |

Ask: "Any concerns with these choices?"

### Section 3: Key Technical Decisions

For each significant decision:
- **Decision:** What we're doing
- **Rationale:** Why this approach
- **Alternatives considered:** What we rejected and why

Ask: "Any decisions missing?"

### Section 4: Risks & Considerations

Simple risk table:

| Risk | Impact | Mitigation |
|------|--------|------------|
| [What could go wrong] | [How bad] | [How to handle] |

Ask: "Any risks I'm missing?"

**End of Simple Mode** - Skip to Output Format section.

---

## Complex Mode

For complex projects, cover all Simple Mode sections PLUS:

### Section 5: Data Model

#### Entities

For each entity:
```
Entity: [Name]
- [attribute]: [type] - [description]
- [attribute]: [type] - [description]
Lifecycle: [created when] → [modified when] → [deleted when]
```

#### Relationships

```
[Entity A] ──1:N──▶ [Entity B]
[Entity C] ◀──N:N──▶ [Entity D]
```

Describe each relationship in plain language.

#### Storage Approach

- Where does data live? (file, database, API, memory)
- How is it accessed? (ORM, raw queries, SDK)
- Any caching considerations?

Ask: "Does this data model support all the user stories?"

### Section 6: API/Interface Design

For each interface point:

```
Interface: [Name]
Purpose: [What it does]

Operations:
- [operation]: [inputs] → [outputs]
  - Error cases: [what can go wrong]

- [operation]: [inputs] → [outputs]
  - Error cases: [what can go wrong]
```

If REST API:
```
POST /resource
  Request: { field: type }
  Response: { field: type }
  Errors: 400 (validation), 404 (not found), 500 (server)
```

Ask: "Do these interfaces cover all the user journeys?"

### Section 7: Component Interactions

For key flows, create sequence diagrams (text-based):

```
User Journey: [From PRD]

User → Component A: [action]
Component A → Component B: [request]
Component B → Database: [query]
Database → Component B: [data]
Component B → Component A: [response]
Component A → User: [result]
```

Ask: "Does this flow match your mental model?"

### Section 8: Integration Points

If connecting to external services:

| Service | Purpose | Auth Method | Rate Limits |
|---------|---------|-------------|-------------|
| [API name] | [why we use it] | [API key, OAuth, etc] | [if any] |

Document:
- How we handle service failures
- Any data we cache
- Retry strategies

### Section 9: Testing Strategy

Define the testing approach:

| Test Level | Coverage | Tools |
|------------|----------|-------|
| Unit | [What to unit test] | [Framework] |
| Integration | [Integration points to test] | [Framework] |
| E2E | [Critical user flows] | [Playwright/etc] |

**Test data strategy:**
- How will test data be created/managed?
- Any fixtures or factories needed?

Ask: "Does this testing approach give you confidence?"

### Section 10: Deployment Approach

| Aspect | Approach |
|--------|----------|
| **Deployment method** | [CI/CD, manual, blue/green] |
| **Feature flags** | [Yes/No - if yes, which flags?] |
| **Rollout strategy** | [All at once, percentage, by region] |
| **Rollback trigger** | [What conditions trigger rollback?] |

Ask: "How do you want to deploy this?"

### Section 11: Observability

How will you know it's working in production?

| Type | What to Track | Tool |
|------|---------------|------|
| **Logs** | [Key events to log] | [Logger] |
| **Metrics** | [Business/technical metrics] | [Tool] |
| **Alerts** | [Conditions to alert on] | [Tool] |
| **Dashboards** | [What to visualize] | [Tool] |

### Section 12: Environment Configuration

| Setting | Dev | Staging | Prod |
|---------|-----|---------|------|
| [Config key] | [value] | [value] | [value] |
| [Secret] | [how stored] | [how stored] | [how stored] |

**Secrets management:**
- Where are secrets stored? (env vars, vault, etc.)
- How are they rotated?

### Section 13: Detailed Risks & Mitigations

Expand the risk analysis:

**Performance:**
- Expected load
- Bottlenecks identified
- Optimization strategies

**Security:**
- Authentication approach
- Authorization model
- Data protection (at rest, in transit)

**Reliability:**
- Failure modes
- Recovery strategies
- Data backup approach

**End of Complex Mode**

---

## Output Format

Save to: `docs/plans/YYYY-MM-DD-<feature>-trd.md`

```markdown
# [Feature Name] - Technical Requirements

**Created:** YYYY-MM-DD
**Status:** Draft
**PRD:** [link to PRD document]
**Complexity:** Simple | Complex

---

## Architecture Overview

[2-3 paragraphs describing the approach]

```
[Optional: ASCII diagram of components]
```

---

## Technology Choices

| Component | Choice | Rationale |
|-----------|--------|-----------|
| Language | [choice] | [why] |
| Framework | [choice] | [why] |
| Database | [choice] | [why] |
| ... | ... | ... |

---

## Key Decisions

### Decision 1: [Topic]

**Decision:** [What we're doing]

**Rationale:** [Why this approach]

**Alternatives considered:**
- [Option A]: Rejected because [reason]
- [Option B]: Rejected because [reason]

### Decision 2: [Topic]
...

---

<!-- Complex mode only - include if applicable -->

## Data Model

### Entities

#### [Entity Name]
| Attribute | Type | Description |
|-----------|------|-------------|
| id | UUID | Primary key |
| ... | ... | ... |

**Lifecycle:** Created when [x], modified when [y], deleted when [z]

### Relationships

```
[Diagram]
```

- [Entity A] has many [Entity B] because [reason]
- ...

### Storage

- **Location:** [Where data lives]
- **Access:** [How we query it]
- **Caching:** [Strategy if any]

---

## API Design

### [Interface/Endpoint Name]

**Purpose:** [What it does]

**Operations:**

#### [Operation Name]
- **Input:** `{ field: type }`
- **Output:** `{ field: type }`
- **Errors:** [Error cases]

---

## Component Interactions

### [User Journey Name]

```
[Sequence diagram]
```

---

## Integration Points

| Service | Purpose | Auth | Notes |
|---------|---------|------|-------|
| ... | ... | ... | ... |

---

## Testing Strategy (Complex)

| Test Level | Coverage | Tools |
|------------|----------|-------|
| Unit | [What] | [Framework] |
| Integration | [What] | [Framework] |
| E2E | [Flows] | [Tool] |

**Test data:** [Strategy]

---

## Deployment (Complex)

| Aspect | Approach |
|--------|----------|
| Method | [CI/CD, manual] |
| Feature flags | [Yes/No] |
| Rollout | [Strategy] |
| Rollback trigger | [Conditions] |

---

## Observability (Complex)

| Type | What | Tool |
|------|------|------|
| Logs | [Events] | [Tool] |
| Metrics | [KPIs] | [Tool] |
| Alerts | [Conditions] | [Tool] |

---

## Environment Config (Complex)

| Setting | Dev | Prod |
|---------|-----|------|
| [Key] | [value] | [value] |

**Secrets:** [Where stored, how rotated]

<!-- End complex mode sections -->

## Risks & Mitigations

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| [Risk] | High/Med/Low | High/Med/Low | [Strategy] |

### Performance Considerations
- [Notes]

### Security Considerations
- [Notes]

---

## Confirmed

- [ ] Architecture confirmed
- [ ] Technology choices confirmed
- [ ] Data model confirmed (complex only)
- [ ] API design confirmed (complex only)
- [ ] Testing strategy confirmed (complex only)
- [ ] Deployment approach confirmed (complex only)
- [ ] Risks acknowledged

**TRD Status:** Draft | Confirmed | In Progress | Completed | Archived

> **Status Lifecycle:**
> - **Draft** - Being written
> - **Confirmed** - Design validated, ready for implementation
> - **In Progress** - Implementation underway
> - **Completed** - Feature shipped
> - **Archived** - Moved to `docs/archive/` after completion
```

---

## After TRD Completion

Once all sections are confirmed:

1. **Commit the TRD:**
   ```bash
   git add docs/plans/YYYY-MM-DD-<feature>-trd.md
   git commit -m "docs: add TRD for <feature>"
   ```

2. **Proceed based on complexity:**

   ### Simple Project Path
   > "TRD complete! Ready to break this into tasks?"
   >
   > **Next:** `superpowers:writing-plans` - Create detailed task breakdown

   ### Complex Project Path
   > "TRD complete! Let's validate this design with multiple AI perspectives."
   >
   > **Next:** `mcp__pal__consensus` - Multi-model design review
   >
   > Use this prompt for consensus:
   > ```
   > Review this technical design for [feature]:
   > - Architecture: [summary]
   > - Key decisions: [list]
   > - Risks identified: [list]
   >
   > Evaluate: Is this architecture sound? Are there better alternatives?
   > What risks are we missing?
   > ```
   >
   > **Then:** `superpowers:writing-plans` - Create detailed task breakdown

---

## Key Principles

- **Adaptive depth** - Don't over-document simple projects
- **PRD traceability** - Every technical decision should trace to a requirement
- **Decision rationale** - Future-you needs to know WHY, not just WHAT
- **Realistic risks** - Better to acknowledge risks than be surprised
- **Diagrams over prose** - A picture (even ASCII) beats paragraphs
- **Validate complex designs** - Use PAL consensus for > 1 day projects

---

## What Does NOT Belong in TRD

These belong in PRD (`personal:prd`):
- User journeys
- User stories
- Business requirements
- Success metrics
- Scope boundaries

These belong in implementation plan (`superpowers:writing-plans`):
- Specific code to write
- File paths
- Step-by-step instructions
- Test cases

The TRD answers: **How are we building it?**
The implementation plan answers: **What exact steps will we take?**

---

## Post-Implementation Learnings

**After the feature is shipped**, update the TRD with learnings:

```markdown
## Post-Implementation Learnings

**Date completed:** YYYY-MM-DD

### What Worked Well
- [Pattern/decision that proved valuable]

### What Would We Do Differently
- [Hindsight improvements]

### Patterns to Extract
- [Reusable patterns for future projects]

### Technical Debt Introduced
- [Known shortcuts that need future attention]
```

This builds organizational memory. Consider also maintaining a decision log:
```
docs/decisions/
  YYYY-MM-DD-decision-title.md
```

---

## Integration with Execution Phase

After planning is complete, your execution loop will be:

```
[Select task from plan]
        ↓
superpowers:test-driven-development  → Write failing test
        ↓
superpowers:executing-plans          → Implement to pass
        ↓
mcp__pal__precommit                  → Validate changes
        ↓
Git commit                           → Save progress
        ↓
← Loop back to [Select task]
```

See [WORKFLOW.md](../../WORKFLOW.md) for the complete workflow.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/solstice035) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
