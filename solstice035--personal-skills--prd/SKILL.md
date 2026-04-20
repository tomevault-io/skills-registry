---
name: prd
description: Create a Product Requirements Document - define WHAT and WHY before technical decisions. Use after brainstorming to formalize user journeys, stories, and acceptance criteria. Use when this capability is needed.
metadata:
  author: solstice035
---

# Product Requirements Document (PRD)

## Workflow Position

```
superpowers:brainstorming  ← You should be here BEFORE using this skill
        ↓
[High uncertainty?] → Spike/Prototype → Validate feasibility
        ↓
    personal:prd           ← YOU ARE HERE
        ↓
    personal:trd           → Next: Define HOW to build it
        ↓                        ↓
        ↑←←←←←←←←←←←←←←←←←←←←←←←←↓
        ↑ [Technical blocker discovered in TRD?]
        ↑ Return to PRD with constraints
        ↓
    [Complex?] → mcp__pal__consensus → Design validation
        ↓
    [Touches auth/payments/PII?] → mcp__pal__secaudit
        ↓
superpowers:writing-plans  → Then: Break into tasks
```

**This skill is Step 2 of the Planning Phase.** See [WORKFLOW.md](../../WORKFLOW.md) for the complete workflow.

---

## Overview

Transform brainstorming output into a structured PRD focused purely on business requirements. This skill helps you think through the WHAT and WHY before any technical decisions.

**This skill is business-focused only.** Architecture, tech stack, and data models belong in the TRD (`personal:trd`).

---

## When to Use

- After `superpowers:brainstorming` when you have a validated design idea
- When starting a new feature/project and need to clarify requirements
- When scope is creeping and you need to re-anchor on core requirements

## When to Skip

**Explicit skip criteria (ALL must be true):**
- Bug fix with known cause AND known solution → Skip
- Config-only change (env vars, settings) → Skip
- Documentation-only change → Skip

**Everything else → At minimum, create a light PRD**

⚠️ "Simple" is a slippery slope. When in doubt, do at least a quick PRD.

---

## The Process

### Step 0: Determine Complexity

Ask the user:

> **How complex is this project?**
>
> **Simple** - Clear requirements, < 1 day to build, familiar problem
> - You'll get: Streamlined PRD with essential sections only
>
> **Complex** - Multiple user types, unclear requirements, > 1 day to build
> - You'll get: Full PRD with detailed journeys and acceptance criteria

**Auto-escalate to Complex if ANY of these apply:**
- New external API/service integrations
- New data models or database changes
- Authentication or authorization changes
- User-facing state management
- Payment or PII handling
- Multiple user types or roles

Wait for answer before proceeding.

---

### Step 0b: High Uncertainty Check (Optional)

If the user is unsure whether the idea is technically feasible:

> **Is there high technical uncertainty?** (e.g., "I don't know if this API can do X")
>
> If yes, consider a **timeboxed spike** before committing to full planning:
> - Set a time limit (e.g., 2 hours)
> - Validate the risky assumption
> - Return with findings before continuing PRD

This prevents wasted planning effort on impossible approaches.

---

## Simple Mode

For simple projects, cover these sections quickly:

### Section 1: Problem Statement (Simple)

One sentence each:
- **What problem?**
- **Why it matters?**
- **What's the user doing today?**

Present and confirm: "Does this capture it?"

### Section 2: User Stories (Simple)

Create 2-5 user stories with acceptance criteria:

```
As a [user], I want [goal], so that [benefit]
- AC: [measurable condition]
```

Prioritize as MVP (must have) or v2 (nice to have).

### Section 3: Scope (Simple)

Quick bullets:
- **In scope:** [what we're building]
- **Out of scope:** [what we're not building]

**End of Simple Mode** - Skip to Output Format section.

---

## Complex Mode

For complex projects, cover all sections thoroughly:

### Step 1: Check for Context

First, look for existing brainstorming output:
- Check `docs/plans/` for recent `*-design.md` files
- If found, use it as input context
- If not found, ask clarifying questions about the idea

### Step 2: Problem Statement

Ask the user to articulate:
- **What problem are we solving?** (one sentence)
- **Why does this matter?** (impact if solved)
- **What happens if we don't solve it?** (cost of inaction)

Present your understanding and ask: "Does this capture the problem?"

### Step 3: User Definition

Even for personal projects, define the user:
- **Who is the user?** (can be "me for personal automation")
- **What's their context?** (when/where do they encounter this problem)
- **What do they currently do?** (existing workaround or manual process)

Ask: "Is this the right user profile?"

### Step 4: User Journeys

Map out the key flows **one at a time**:

```
Journey: [Name]
1. Entry point: How does the user start?
2. Actions: What steps do they take?
3. Decision points: Where do they make choices?
4. Exit point: How do they know they're done?
```

For each journey, ask: "Does this flow match your mental model?"

**Start with the happy path.** Only add error/alternative paths if the user identifies them as important.

### Step 5: User Stories

Convert journeys into prioritized stories:

**Format:**
```
As a [user], I want [goal], so that [benefit]
- Acceptance Criteria: [measurable condition]
```

**Prioritize:**
- **MVP (Must Have):** Without these, the project has no value
- **v2 (Nice to Have):** Useful but can wait

Present stories in batches of 3-5, ask: "Are these prioritized correctly?"

### Step 6: Success Metrics

Define how you'll know it works:
- **Functional:** What behavior proves it works?
- **Qualitative:** How will you feel using it?
- **Measurable:** Any numbers that matter? (even for personal use)

Ask: "What would make you say 'this is done'?"

### Step 7: Scope Boundaries

Explicitly define boundaries:
- **In Scope (MVP):** What we're building now
- **Out of Scope (v2):** Explicitly deferred
- **Assumptions:** What we're taking for granted
- **Dependencies:** What needs to exist first

This is critical for preventing scope creep.

---

## Output Format

Save to: `docs/plans/YYYY-MM-DD-<feature>-prd.md`

```markdown
# [Feature Name] - Product Requirements

<!--
  SIMPLE MODE: Only fill out Problem Statement, User Stories (MVP), and Scope.
  COMPLEX MODE: Fill out all sections.
-->

**Created:** YYYY-MM-DD
**Status:** Draft
**Complexity:** Simple | Complex
**Design Doc:** [link to brainstorming output if exists]

---

## Problem Statement

**Problem:** [One sentence]

**Why it matters:** [Impact if solved]

**Cost of inaction:** [What happens if we don't solve it]

---

## User Definition

**User:** [Who]

**Context:** [When/where they encounter this]

**Current workaround:** [What they do today]

---

## User Journeys

### Journey 1: [Primary Happy Path]

1. **Entry:** [How user starts]
2. **Action:** [What they do]
3. **Decision:** [Any choices]
4. **Exit:** [How they know they're done]

### Journey 2: [Secondary Path] (if applicable)
...

---

## User Stories

### MVP (Must Have)

- [ ] **US-001:** As a [user], I want [goal], so that [benefit]
  - **AC:** [Acceptance criteria]

- [ ] **US-002:** ...

### v2 (Nice to Have)

- [ ] **US-010:** As a [user], I want [goal], so that [benefit]
  - **AC:** [Acceptance criteria]

---

## Success Metrics

| Metric | Target | How to Measure |
|--------|--------|----------------|
| [Functional] | [Expected behavior] | [Verification method] |
| [Qualitative] | [Feeling/experience] | [Self-assessment] |

---

## Scope

### In Scope (MVP)
- [Specific deliverable 1]
- [Specific deliverable 2]

### Out of Scope (v2)
- [Deferred feature 1]
- [Deferred feature 2]

### Assumptions
- [What we're taking for granted]

### Dependencies
- [What needs to exist first]

### Constraints from Technical Discovery
<!-- Added during TRD phase if technical blockers are found -->
- [List any technical constraints discovered during the TRD phase that impact requirements]

---

## Confirmed

- [ ] Problem statement confirmed
- [ ] User definition confirmed
- [ ] User journeys confirmed
- [ ] User stories prioritized
- [ ] Scope boundaries accepted

**PRD Status:** Draft | Confirmed | In Progress | Completed | Archived

> **Status Lifecycle:**
> - **Draft** - Being written
> - **Confirmed** - Requirements validated, ready for TRD
> - **In Progress** - Implementation underway
> - **Completed** - Feature shipped
> - **Archived** - Moved to `docs/archive/` after completion
```

---

## After PRD Completion

Once all sections are approved:

1. **Commit the PRD:**
   ```bash
   git add docs/plans/YYYY-MM-DD-<feature>-prd.md
   git commit -m "docs: add PRD for <feature>"
   ```

2. **Proceed to next step:**

   > "PRD complete! Ready to define the technical approach?"
   >
   > **Next:** `personal:trd` - Define architecture, data models, and tech choices
   >
   > **Complexity Decision:**
   > - Simple project? → `personal:trd --simple` then `superpowers:writing-plans`
   > - Complex project? → `personal:trd --complex` then `mcp__pal__consensus` for design review

---

## Key Principles

- **One section at a time** - Don't overwhelm, validate incrementally
- **Business language only** - No technical jargon in PRD
- **User-centric** - Everything framed from user perspective
- **Explicit scope** - What's OUT is as important as what's IN
- **Defer v2** - Be ruthless about MVP scope
- **Stories have acceptance criteria** - Vague stories cause scope creep
- **Complexity-appropriate** - Don't over-document simple projects

---

## What Does NOT Belong in PRD

These belong in TRD (`personal:trd`):
- Architecture decisions
- Technology choices
- Data models
- API design
- Database schema
- Performance requirements
- Security implementation details

The PRD answers: **What are we building and why?**
The TRD answers: **How are we building it?**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/solstice035) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
