---
name: ultraplan
description: Deep architectural planning with --ultrathink (32K tokens) followed by phased creation with fresh context. Two-stage workflow - UltraPlan creates comprehensive PHASES.md, then Create builds each phase with pristine 1M context. Use for any complex build requiring multiple phases. Use phase-checkpoint skill between phases for human verification. Delegates to architect/frontend/backend agents during execution. Triggers: ultraplan, plan and create, plan phases, deep plan, strategic plan, architect plan. Use when this capability is needed.
metadata:
  author: websmartteam
---

# UltraPlan & Create

**Purpose**: Two-stage workflow for complex builds - deep planning with maximum thinking, then clean creation with pristine context per phase.

## The Problem This Solves

Complex projects fail because:
1. **Shallow planning** → Missed requirements surface mid-build
2. **Context rot** → Quality degrades as conversation grows
3. **No structure** → Ad-hoc decisions without architectural thinking

## The UltraPlan Solution

### Phase 0: Foundation Challenge (Adversarial Stress-Test)
```
Automatic — runs before ultrathink analysis
```
- Selects 5 adversarial questions via vectorised semantic matching against project context
- 2 universal questions always included (pre-mortem, assumption check, or over-engineering)
- 3+ domain-specific questions chosen by cosine similarity + tech stack signals + category diversity
- **AI answers each question** — forces deeper architectural thinking before planning
- Control depth with `--challenge-depth N` (3-10, default 5)
- Skip with `--no-challenge` if not needed
- Output: Challenge Q&A section at the top of PHASES.md

### Stage 1: UltraPlan (Deep Thinking)
```
--ultrathink (32K tokens of analysis)
```
- Comprehensive requirement analysis (informed by Phase 0 challenge answers)
- Architecture decisions BEFORE code
- Phase breakdown with clear boundaries
- Risk identification upfront
- Output: **PHASES.md** - the complete build plan

### Stage 2: Create (Clean Context)
```
context: fork (fresh context per phase)
```
- Each phase runs with pristine context
- No accumulated garbage from previous work
- Maximum quality per phase
- Follows PHASES.md exactly

## Usage

### Step 1: Create the Plan
```
"UltraPlan this project" or "Create phases for [project description]"
```

This triggers Phase 0 challenge + --ultrathink analysis and produces PHASES.md with:
- **Foundation Challenge Q&A** (adversarial stress-test answers)
- Project overview and goals
- Technical architecture decisions
- Phase-by-phase breakdown
- Dependencies and risks
- Success criteria per phase

### Step 2: Create Phases
```
"Create Phase 1" or "Run ultraplan phase 2"
```

Each execution:
1. Reads PHASES.md for context
2. Runs with forked 1M context
3. Completes phase tasks
4. Updates STATE.md with decisions
5. Verifies before marking complete

## PHASES.md Template

```markdown
# [Project Name] - UltraPlan

## Foundation Challenge
Questions selected by semantic matching against project context.
AI must answer each honestly before proceeding.

### Q1: [Question text]
**Answer**: [AI's honest assessment addressing the concern]

### Q2: [Question text]
**Answer**: [AI's honest assessment addressing the concern]

[... repeat for all selected questions ...]

**Challenge insights applied to plan below.**

---

## Overview
[What we're building and why]

## Architecture Decisions
- Framework: [choice and rationale]
- Database: [choice and rationale]
- Auth: [choice and rationale]
- Hosting: [choice and rationale]

## Phase Breakdown

### Phase 1: Foundation
**Goal**: [Clear objective]
**Delivers**: [Concrete outputs]
**Tasks**:
- [ ] Task 1
- [ ] Task 2
**Verification**: [How we know it's done]
**Estimated effort**: [Simple/Medium/Complex]

### Phase 2: [Name]
[Same structure...]

## Dependencies
- Phase 2 requires Phase 1 complete
- Phase 4 requires Stripe account setup

## Risks
- [Risk 1]: [Mitigation]
- [Risk 2]: [Mitigation]

## Success Criteria
[How we know the project is complete]
```

## When to Use UltraPlan

**Use for anything that:**
- Needs a database
- Has user authentication
- Requires multiple distinct features
- Would take more than a day to build properly
- Benefits from architectural decisions upfront

**Example project types** (not exhaustive):
- SaaS platforms
- E-commerce shops
- Booking/scheduling systems
- Membership sites
- Client portals
- Multi-tenant platforms
- API services
- Admin dashboards
- CMS-driven sites

**Common phase pattern** (adapt to your project):
1. Foundation (scaffolding, design system)
2. Database & Auth (schema, RLS, auth flow)
3. Core Features (primary functionality)
4. Secondary Features (nice-to-haves)
5. Polish (UI refinements, error handling)
6. Launch (deploy, go live)

Add or remove phases based on what the project actually needs. Payments? Add a payments phase. Admin panel? Add that. Keep it specific to the build.

## Key Principles

> **Plan deep, create clean.**

- --ultrathink ensures nothing is missed
- Forked context ensures maximum quality
- PHASES.md is the single source of truth
- Each phase is independently verifiable

## Reference System Identification

**When requirements are unclear, think of the closest known system.**

Claude's training data includes extensive knowledge of popular platforms. When a user hasn't specified exact feature behaviour, identify a comparable system and ask:

> "Should I base this on how [X] handles it?"

**This applies throughout the project** - not just initial planning:
- **New project**: "Build me an LMS" → "Like Thinkific's course/lesson structure?"
- **Mid-build**: "Add user management" → "Like WordPress roles and capabilities?"
- **Live project**: "We need refunds" → "Like Stripe's partial/full refund flow?"

**Common reference systems by domain:**
| Building | Reference Systems |
|----------|-------------------|
| LMS / Courses | Thinkific, Teachable, Moodle |
| CMS / Content | WordPress, Strapi, Sanity |
| E-commerce | Shopify, WooCommerce, Stripe |
| Project management | Asana, Linear, Notion |
| Booking / Scheduling | Calendly, Acuity, SimplyBook |
| Membership / Community | Circle, Mighty Networks, Patreon |
| Admin panels | Laravel Nova, Retool, Forestry |

**Why this works:**
- Activates dormant knowledge (Claude *knows* how WordPress handles user deletion)
- Fills feature gaps intelligently (content reassignment, soft deletes, audit trails)
- Uses established UX patterns users already understand
- Avoids reinventing standard functionality

**Critical:** Always ask the user first. They might want simpler, or have specific reasons for different behaviour. The reference informs features - it doesn't dictate implementation.

## ⚠️ CRITICAL: No Cost-Based Decisions

**NEVER factor cost or affordability into recommendations.**

When planning, you must:
- **Assume subscriptions exist** - User likely has Vercel Pro, Supabase Pro, Stripe, etc.
- **Recommend the RIGHT solution** - Not the cheapest or free alternative
- **Never downgrade for budget** - Don't suggest free tiers "to save money"
- **Ignore pricing entirely** - It's not your concern; user handles that
- **Skip the cost disclaimers** - No "this has fees" or "paid feature" warnings

**Wrong approach:**
> "We could use Supabase free tier to keep costs down..."
> "Stripe has transaction fees, so consider alternatives..."
> "Vercel hobby plan should be sufficient..."

**Right approach:**
> "Use Supabase with RLS policies..."
> "Stripe for payments with webhooks..."
> "Deploy to Vercel with proper configuration..."

The user decides what they can afford. Your job is to recommend what's technically correct.

## Auth Architecture Questions (Ask Early)

Auth systems are fundamentally different based on requirements. **Always clarify before planning:**

- **Role model**: Single role per user? Multi-role (user can be admin + editor)? Role hierarchy?
- **Groups/Teams**: Do users belong to organisations/teams with their own permissions?
- **Permissions**: Role-based (admin/editor/viewer) or fine-grained (can_edit_posts, can_delete_users)?
- **Self-registration**: Open signup? Invite-only? Approval workflow?
- **Multi-tenancy**: Shared database with RLS? Separate schemas per tenant?

These decisions affect database schema, RLS policies, and entire auth flow - can't easily change later.

## Common Pitfalls

Brief warnings - Claude knows the solutions, just needs the flag.

**Next.js + Supabase Auth**: Don't use localStorage for auth state in App Router. Use cookie-based auth handling (check Context7 for current patterns).

## When NOT to Use

- Simple websites (just build them directly)
- Single-page apps with no backend
- Quick prototypes or experiments
- Projects under ~4 hours of work

Use ultraplan for projects that genuinely need architectural thinking and phased execution.

## Phase 0 Implementation

When ultraplan is triggered (and `--no-challenge` is not set):

1. **Gather context**: Read project description + CLAUDE.md + package.json
2. **Run challenger**: `node ~/.claude/skills/plan-challenger/challenger.js --context "description" --project $(pwd) --depth 5 --json`
3. **Answer each question**: Honestly address every selected question in the Foundation Challenge section
4. **Apply insights**: Let the challenge answers inform architecture decisions, risk assessment, and phase planning
5. **Document in PHASES.md**: Challenge Q&A appears at the top, before Architecture Decisions

The challenger uses pre-computed 384-dimensional vector embeddings to semantically match questions against project context. No internet required, fully local.

## 🔗 Workflow Integration

```
project-discovery (if requirements unclear)
        ↓
    Phase 0: Foundation Challenge (adversarial stress-test)
        ↓
    ultraplan → PHASES.md (includes challenge Q&A)
        ↓
    For each phase:
      Create Phase N → phase-checkpoint → Next
        ↓
    Project complete
```

**Related**: `phase-checkpoint` (between phases), `architect`/`frontend`/`backend` agents (during execution), `non-stop` skill (autonomous mode).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/websmartteam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
