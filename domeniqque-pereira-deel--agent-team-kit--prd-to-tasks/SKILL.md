---
name: prd-to-tasks
description: > Use when this capability is needed.
metadata:
  author: domeniqque-pereira-deel
---

# PRD to Tasks — Implementation Plan Generator

Transform a Product Requirements Document into a sequenced, implementable task list
optimized for a solo developer with agent assistance.

## Input Requirements

Before generating tasks, you MUST have:
1. The PRD document (read it fully)
2. The target tech stack (default: Expo + Supabase + TypeScript)
3. Knowledge of existing project structure (check filesystem)

## Task Generation Process

### Step 1: Extract Workstreams
Read the PRD and identify distinct workstreams:
- **Data Model** — What entities, relationships, and schemas are needed?
- **Backend Logic** — What API routes, edge functions, or server logic?
- **Authentication** — What auth flows are required?
- **Navigation** — What screens and navigation structure?
- **UI Components** — What reusable components are needed?
- **Screen Implementation** — What screens with what behavior?
- **State Management** — What client state is needed?
- **Third-party Integrations** — What external APIs or services?
- **Asset Pipeline** — What images, fonts, or static assets?

### Step 2: Sequence with Dependencies
Apply this universal build order:

```
1. Environment & Config       (env vars, app.config.ts, packages)
2. Data Model & Types         (TypeScript types, Supabase schema)
3. Database & Migrations      (tables, RLS, indexes)
4. Authentication             (auth setup, protected routes)
5. API Layer                  (Supabase client, edge functions, queries)
6. Shared Components          (design system, reusable UI)
7. Navigation Structure       (layouts, tab bar, stacks)
8. Screen Implementation      (feature screens, one at a time)
9. State & Data Flow          (stores, context, real-time subscriptions)
10. Polish & Edge Cases       (error handling, loading states, empty states)
11. Testing                   (critical paths, happy paths)
12. Review & Optimization     (Devil's Advocate, performance)
```

### Step 3: Generate Task Cards
Each task MUST follow this format:

```markdown
### Task [phase].[number]: [Clear action verb] [what]
- **Size**: XS / S / M / L
- **Agent**: team-lead / expo-developer / backend-engineer / ui-specialist
- **Depends on**: [task IDs or "none"]
- **Files**: [expected files to create/modify]
- **Acceptance Criteria**:
  - [ ] [Specific, verifiable condition]
  - [ ] [Another condition]
- **Notes**: [Any context, gotchas, or decisions]
```

### Step 4: Output Format

Save the task list to `docs/tasks/[feature-name]-tasks.md` with this structure:

```markdown
# Implementation Plan: [Feature Name]

**Source PRD**: [path to PRD]
**Generated**: [date]
**Estimated Effort**: [total time estimate]
**Risk Level**: Low / Medium / High

## Summary
[2-3 sentence overview of what we're building]

## Architecture Decisions
- [Key decision 1 and why]
- [Key decision 2 and why]

## Phase 1: Foundation
### Task 1.1: ...
### Task 1.2: ...

## Phase 2: Core
### Task 2.1: ...
...

## Phase N: Review
### Task N.1: Devil's Advocate full review
- **Agent**: devils-advocate
- **Acceptance Criteria**:
  - [ ] All code reviewed for security issues
  - [ ] Architecture consistency verified
  - [ ] Performance red flags identified
  - [ ] UX issues flagged

## Dependency Graph
[ASCII or mermaid diagram showing task dependencies]

## Risk Register
| Risk | Impact | Mitigation |
|------|--------|------------|
| [risk] | High/Med/Low | [mitigation] |
```

## Sizing Guidelines

| Size | Time | Examples |
|------|------|---------|
| XS | < 5 min | Add env var, install package, update config |
| S | 5-15 min | Create type file, simple utility, basic component |
| M | 15-30 min | Full screen, API integration, migration + RLS |
| L | 30-60 min | Complex screen with state, multi-table migration |

**Rule**: No task should be larger than L. If it is, break it down further.

## PRD Quality Checks

Before generating tasks, verify the PRD covers:
- [ ] Clear user stories or feature descriptions
- [ ] Data model (even rough)
- [ ] Screen list with rough wireframes or descriptions
- [ ] Authentication requirements
- [ ] Third-party service dependencies
- [ ] MVP scope clearly defined (what's in, what's out)

If the PRD is missing critical info, ask the developer before proceeding.
Don't guess — gaps in the PRD become bugs in the code.

## Anti-Patterns to Avoid

- **Don't front-load everything**: Avoid 20 foundation tasks before any visible progress
- **Don't create tasks for obvious things**: "Install TypeScript" is not a task if the project already has it
- **Don't over-specify implementation**: Tasks should say WHAT, not HOW (the agent decides HOW)
- **Don't ignore the happy path**: First task for each screen should be the happy path, edge cases come later
- **Don't bundle unrelated work**: "Set up auth and create profile screen" is two tasks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/domeniqque-pereira-deel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
