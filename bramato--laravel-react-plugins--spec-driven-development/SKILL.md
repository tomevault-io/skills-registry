---
name: spec-driven-development
description: > Use when this capability is needed.
metadata:
  author: bramato
---

# Spec-Driven Development Skill

A structured, human-in-the-loop development methodology adapted from the Kiro workflow. Ensures every feature moves through Requirements → Design → Tasks → Implementation with explicit approval gates and full traceability.

**Target users:** Full-stack Laravel + React developers building features that benefit from upfront planning, or teams that need formal specifications.

## Core Philosophy

Built on pragmatic software engineering principles:

1. **Collaboration is mandatory** — No code changes proceed without explicit user approval at critical phases
2. **Simplicity over complexity** — Don't over-engineer, don't over-abstract, don't overcomplicate
3. **Pragmatism over perfection** — Solutions that work in practice beat theoretical elegance
4. **Design authority** — The approved design document is the single source of truth for implementation
5. **No scope creep** — Forbidden to add features, classes, or endpoints not in the approved design

See [references/design-principles.md](references/design-principles.md) for the full philosophy.

## Four-Phase Workflow

```
Phase 1: /spec          Phase 2: /design         Phase 3: /plan-tasks     Phase 4: /implement
┌─────────────┐        ┌─────────────┐          ┌─────────────┐          ┌─────────────┐
│ requirements │──✓──▶  │  design.md  │──✓──▶    │  tasks.md   │──────▶   │   Execute   │
│     .md      │        │             │          │             │          │  task by    │
│              │        │ Architecture│          │ Hierarchical│          │  task       │
│ User Stories │        │ Data Flow   │          │ Checkboxes  │          │             │
│ EARS syntax  │        │ API Specs   │          │ Requirement │          │ Context     │
│ BE + FE      │        │ DB Schema   │          │ Tracing     │          │ gathering   │
│              │        │ Components  │          │             │          │ mandatory   │
└─────────────┘        │ Security    │          └─────────────┘          └─────────────┘
                       │ Test Plan   │
       ▲               └─────────────┘                                          │
       │                      ▲                                                 │
   APPROVAL               APPROVAL                                         Mark [x]
    GATE                    GATE                                          in tasks.md
```

## Directory Convention

All spec artifacts live in a feature-specific directory:

```
.kiro/
├── specs/
│   └── [feature-name]/
│       ├── requirements.md    ← Phase 1 output
│       ├── design.md          ← Phase 2 output
│       └── tasks.md           ← Phase 3 output
└── steering/
    └── [project context files — coding standards, architecture decisions, etc.]
```

**Naming:** Use kebab-case for feature names (e.g., `order-management`, `user-authentication`).

## Phase 1: Requirements (EARS Syntax)

### Template: requirements.md

```markdown
# [Feature Name] — Requirements

## Introduction

**Problem:** [What problem does this feature solve?]
**Objectives:** [What are the goals?]
**Project Alignment:** [How does this fit into the broader project?]

## Requirements

### Backend Requirements

**1. [Requirement title]**

**As a** [role], **I want** [feature], **so that** [benefit]

Acceptance Criteria:
1. WHEN [trigger], THEN [expected outcome]
2. IF [condition], THEN [expected result]
3. WHERE [constraint applies], THEN [required action]

### Frontend Requirements

**2. [Requirement title]**

**As a** [role], **I want** [UI feature], **so that** [benefit]

Acceptance Criteria:
1. WHEN [user action], THEN [UI response]
2. IF [state condition], THEN [visual feedback]
3. WHILE [loading/processing], THEN [interim UI state]
```

### EARS Syntax Reference

EARS (Easy Approach to Requirements Syntax) provides unambiguous acceptance criteria:

| Keyword | Purpose | Example |
|---------|---------|---------|
| `WHEN` | Trigger/event | WHEN the user submits the order form, THEN create an Order record |
| `THEN` | Expected outcome | THEN display a success toast with the order number |
| `IF` | Conditional logic | IF the cart total exceeds the stock, THEN show an error |
| `WHERE` | Constraint | WHERE the user role is 'admin', THEN allow bulk delete |
| `WHILE` | Concurrent state | WHILE the payment is processing, THEN show a spinner and disable the button |

See [references/ears-syntax.md](references/ears-syntax.md) for comprehensive examples.

### Requirements Best Practices

- **Proactively** think through edge cases and error conditions — don't ask multiple clarifying questions
- Generate an initial draft immediately, then iterate
- Separate backend (Laravel: API, DB, business logic) from frontend (React/Inertia: UI, UX, state) requirements
- Number requirements sequentially (1, 2, 3...) for traceability in later phases
- Each requirement should be independently testable

### Approval Gate

After presenting requirements, use exactly:

> **"Do the requirements look good? If so, we can move on to the technical design phase."**

Do not proceed to Phase 2 without explicit user approval. Iterate until sign-off.

## Phase 2: Technical Design

### Prerequisites

- Explicit user approval of `requirements.md`
- Read all files in `.kiro/steering/` for project context
- Static analysis of existing codebase

### Template: design.md

The design document has **9 mandatory sections**:

```markdown
# [Feature Name] — Technical Design

## 1. Architectural Overview
[High-level description of the solution and how it fits into the existing system]

## 2. Data Flow Diagram
[Mermaid.js diagram showing data movement between components]

## 3. Service Provider Artifacts
[For each entity: list all artifacts to create]
- Model, DTO, Service, Controller, FormRequest, Policy, ServiceProvider
- Events/Listeners if applicable

## 4. API / Route Definitions
[For Inertia: Laravel routes that render Inertia pages]
[For API endpoints: method, path, request body, response structure]

## 5. Database Schema
[Laravel migration code for new/modified tables]
[Indexes, foreign keys, constraints]

## 6. React Components (Inertia Pages)
[Component hierarchy, props interface, state management]
[Which Inertia pages to create: Index, Show, Create, Edit]

## 7. TypeScript Interfaces
[Interfaces matching controller props / API responses]

## 8. Security Considerations
[Input validation, authentication, authorization policies]
[CSRF, XSS prevention, mass assignment protection]

## 9. Test Strategy
[Unit tests: Services, DTOs, Models]
[Feature tests: HTTP endpoints, Inertia responses]
[Component tests: React pages and components]
```

### Design Best Practices

- Reference existing code patterns in the codebase
- Use Mermaid.js for data flow diagrams
- Specify exact file paths for each artifact to create
- Include TypeScript `interface` or `type` definitions for the frontend contract
- Map every design element back to a requirement number

### Approval Gate

After presenting the design, use exactly:

> **"Does the technical design look good? If so, we can proceed to implementation planning."**

Do not proceed to Phase 3 without explicit user approval.

## Phase 3: Task Planning

### Prerequisites

- Explicit user approval of `design.md`

### Template: tasks.md

```markdown
# [Feature Name] — Implementation Tasks

- [ ] 1. Create database migration and Model
  - Migration with columns, indexes, foreign keys
  - Eloquent Model with $fillable, $casts, relationships, scopes
  - _Requirements: 1.1, 1.2_

- [ ] 2. Create Service Provider artifacts
  - DTO with fromRequest(), fromModel(), toArray()
  - Service with business logic methods
  - Controller with Inertia::render() responses
  - StoreRequest, UpdateRequest with validation rules
  - Policy with authorization rules
  - ServiceProvider registration
  - _Requirements: 1.3, 1.4, 2.1_

- [ ] 3. Create Inertia React pages
  - Index page with list/table
  - Show page with detail view
  - Create/Edit pages with forms
  - TypeScript interfaces for props
  - _Requirements: 3.1, 3.2_

- [ ] 4. Write tests
  - Feature tests for all HTTP endpoints
  - Unit tests for Service and DTO
  - Component tests for React pages
  - _Requirements: all_
```

### Task Planning Rules

- **High-level tasks:** Numbered with checkboxes `- [ ] N.`
- **Sub-tasks:** Indented bullets (no checkboxes, no numbering)
- **Traceability:** Every task ends with `_Requirements: N.N, N.N_`
- **Dependency order:** Tasks listed in logical execution order
- **No approval gate** — Tasks are ready for immediate execution

## Phase 4: Implementation Execution

### Trigger Commands

| Command | Target |
|---------|--------|
| `implement`, `continue`, `next` | First incomplete `- [ ]` task |
| `implement 3`, `run task 3` | Specific numbered task |

### Mandatory Execution Flow

For **every** task, follow this sequence without exception:

1. **State Check** — Read `tasks.md`, identify the target task
2. **Context Gathering** (MANDATORY):
   - Read entire `design.md`
   - Read entire `requirements.md`
   - Read all files in `.kiro/steering/`
   - Summarize each document to prove comprehension
3. **Planning Announcement**:
   - Explain the task's architectural role
   - List applicable requirements
   - Identify specific files to create or modify
4. **Implementation** — Execute code changes following the design
5. **Completion** — Mark task `[x]` in `tasks.md`, report results
6. **Await** — Wait for next command

### Enforcement Rules

| Rule | Description |
|------|-------------|
| **Design Authority** | `design.md` is the authoritative blueprint — no features outside its scope |
| **No Scope Creep** | Forbidden to add undocumented features, classes, or endpoints |
| **File Context** | Always read the complete file, never truncated |
| **Design Compliance** | Every modification must trace back to the approved design |
| **Service Provider Pattern** | All Laravel services must follow the prescribed architecture |

## When to Use This Workflow

| Scenario | Use Spec-Driven? | Alternative |
|----------|------------------|-------------|
| New feature with multiple entities | Yes | — |
| Complex business logic | Yes | — |
| Feature touching 5+ files | Yes | — |
| Simple CRUD for one entity | No | `/scaffold-service` |
| Quick bug fix | No | Direct fix |
| Adding a single field/column | No | `/make-migration` |
| New React component only | No | `/make-component` |

## Related Commands

- `/spec` — Phase 1 entry point
- `/design` — Phase 2 entry point
- `/plan-tasks` — Phase 3 entry point
- `/implement` — Phase 4 entry point
- `/commit` — Commit after completing tasks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bramato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
