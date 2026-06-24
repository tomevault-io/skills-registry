---
name: writing-plans
description: Use BEFORE starting non-trivial work. Creates a structured plan with task breakdown, technical design, and risks. A task is non-trivial if it touches 3+ files, involves a design choice, or will take more than 30 minutes. Use when this capability is needed.
metadata:
  author: cklxx
---

# Writing Plans

**Role in the loop:** THINK before coding. This skill produces the plan. It does NOT record outcomes, persist knowledge, or review past work — those are separate skills.

## Precise Trigger Conditions

Activate this skill when ANY of these are true:

| Signal | Example |
|--------|---------|
| User describes a new feature | "Add OAuth login" |
| Work will touch 3+ files | Refactoring a module that spans multiple packages |
| Multiple valid approaches exist | "Should we use Redis or in-memory cache?" |
| Scope is unclear | "Improve the API performance" |
| User explicitly asks for a plan | "Plan out the migration" |

Do NOT activate when:
- The task is a single-file fix with an obvious solution
- The user says "just do it" or "quick fix"
- You're in the middle of executing an existing plan

## Workflow

### Step 1 — Load context (30 seconds, not 5 minutes)

```
1. Read docs/memory/long-term.md → extract items relevant to this task
2. Grep docs/plans/ for related past plans → extract lessons and failures
3. Read the specific files the user mentioned or that are obviously relevant
```

Do NOT read the entire codebase. Read what's needed for this plan.

If requirements are ambiguous, ask the user. Batch all questions into ONE message:
```
Before I plan this, I need to clarify:
1. Should we support X or only Y?
2. Is Z a hard requirement or nice-to-have?
3. What's the target deadline?
```

### Step 2 — Write the plan

Create `docs/plans/YYYY-MM-DD-<slug>.md`:

```markdown
# Plan: <Title>

> Created: YYYY-MM-DD
> Status: draft
> Trigger: <the user request that prompted this>

## Goal & Success Criteria
- **Goal**: <one sentence>
- **Done when**: <measurable condition>
- **Non-goals**: <what we're NOT doing>

## Current State
- <relevant files/modules and their current behavior>
- <the problem or opportunity driving this work>

## Task Breakdown

| # | Task | Files | Size | Depends On |
|---|------|-------|------|------------|
| 1 | ... | ... | S/M/L | — |
| 2 | ... | ... | S/M/L | T1 |

S = under 30 min. M = 30 min–2 hr. L = over 2 hr (must split).

## Technical Design
- **Approach**: <3–5 sentences>
- **Alternatives rejected**: <what and why>
- **Key decisions**: <decision — rationale>

## Risks

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| ... | H/M/L | H/M/L | ... |

## Verification
- <how to test each task>
- <rollback plan>
```

### Concrete Example

A real plan for "Add rate limiting to the API":

```markdown
# Plan: API Rate Limiting

> Created: 2026-02-09
> Status: draft
> Trigger: User requested rate limiting to prevent abuse

## Goal & Success Criteria
- **Goal**: Add per-IP rate limiting to all public API endpoints.
- **Done when**: Requests exceeding 100/min per IP get HTTP 429, with tests passing.
- **Non-goals**: Per-user rate limiting, admin dashboard for limits.

## Task Breakdown

| # | Task | Files | Size | Depends On |
|---|------|-------|------|------------|
| 1 | Add rate limiter middleware | internal/middleware/ratelimit.go | S | — |
| 2 | Write tests for rate limiter | internal/middleware/ratelimit_test.go | S | T1 |
| 3 | Wire middleware into router | cmd/server/main.go | S | T1 |
| 4 | Add config for rate limit values | config/config.go, config.yaml | S | T1 |

## Technical Design
- **Approach**: Token bucket algorithm using golang.org/x/time/rate. Per-IP tracking with sync.Map, entries expire after 10 min idle. Middleware returns 429 with Retry-After header.
- **Alternatives rejected**: Redis-based (overkill for single-instance), fixed window (bursty edge cases).
- **Key decisions**: Token bucket over sliding window — simpler, well-tested stdlib implementation.

## Risks

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| sync.Map memory growth under attack | M | M | Add TTL-based eviction goroutine |
| Rate limit too aggressive for legitimate users | L | H | Make limits configurable via config.yaml |
```

### Step 3 — Get approval, then execute

- Present the plan to the user.
- Wait for approval before writing code.
- Update `Status: draft` → `Status: in-progress` when starting.
- Update `Status: in-progress` → `Status: completed` when done.
- If reality diverges from the plan, update the plan file FIRST.

### Step 4 — Handoff to other skills

When the plan completes:
- If key decisions were made → trigger `managing-memory` to persist them
- If bugs were hit during execution → `recording-practices` should have already logged them

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cklxx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
