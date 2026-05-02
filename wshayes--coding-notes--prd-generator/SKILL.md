---
name: prd-generator
description: > Use when this capability is needed.
metadata:
  author: wshayes
---

# PRD Generator

Create engineer-focused PRDs with checkbox tasks compatible with the Ralph loop.

## Principles

- **Quality over speed** - Planning is 95% of the work
- **Ralph-compatible** - All tasks use `[ ]` checkbox format
- **Validation-driven** - Run all checks before delivery

## Workflow

### 1. Discovery

Enter plan mode. Ask questions iteratively until all PRD sections can be filled:

**Vision**: Problem solved? Primary user? Success criteria?
**Scope**: MVP features? Explicitly out of scope? Integrations?
**Tech**: Stack? Existing patterns? Deployment constraints?
**User journeys**: Primary flows? Key actions? Feedback needed?
**Data**: What's stored? Key entities? External sources?
**Non-functional**: Performance? Security? Accessibility?
**Constraints**: Timeline? Team? Dependencies?
**Edge cases**: Error states? Recovery?

### 2. Generate PRD

Use the template in [references/prd-template.md](references/prd-template.md).

Key requirements:
- Executive summary: 2-3 sentences
- Out of scope: explicitly defined (never empty)
- Each user story: minimum 3 acceptance criteria
- All tasks: `[ ]` checkbox format
- Tasks: atomic, ordered by dependency, implementation-ready

### 3. Validate

Before delivery, verify:

| Check | Requirement |
|-------|-------------|
| Executive summary | 2-3 sentences exist |
| Out of scope | Explicitly defined |
| Acceptance criteria | 3+ per user story |
| Task format | All use `[ ]` checkboxes |
| Tech stack | Fully specified |
| Language | No "might", "could", "maybe", "TBD" |
| Criteria | All testable/verifiable |
| Data model | Covers all entities |
| Error handling | Approach defined |
| Env vars | Documented |

### 4. Deliver

1. Write PRD to `PRD.md` in project root
2. Create empty `progress.txt` for Ralph
3. Summarize: user stories count, tasks count, phases, open questions

## Ralph Integration

Ralph reads `PRD.md`, implements one `[ ]` task at a time, marks `[x]` only if tests pass, commits with `feat: [task]`. Tasks must be:

- **Self-contained** - Implementable without other incomplete tasks
- **Testable** - Clear pass/fail criteria
- **Ordered** - Dependencies first
- **Atomic** - One commit-sized change

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wshayes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
