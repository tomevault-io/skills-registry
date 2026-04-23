---
name: container-architecture
description: Documentation format, best practices, and guidelines for Component level architecture. Focuses on responsibility breakdown, API contracts, data schemas, and technology stack. No code snippets. Use when this capability is needed.
metadata:
  author: wtah
---

# Container Architecture Skill

This skill defines how the container architect **breaks down responsibilities** into components and **defines contracts**. The container architect receives responsibilities from the high-level architect and distributes them across well-designed components.

**Core Principle**: Define WHAT components do, not HOW. No code snippets. Document technology from constraints.

---

## Success Factors

### 1. High-Quality Responsibility Breakdown

| Criterion | Measure |
|-----------|---------|
| **Complete Coverage** | Every responsibility assigned to exactly one component |
| **No Overlap** | No responsibility in multiple components |
| **Traceable** | Every responsibility links to FR/AI/NFR/UJ source |
| **Single Purpose** | Each component has ONE clear responsibility |

### 2. Clear API Contracts

| Criterion | Measure |
|-----------|---------|
| **All Operations Defined** | Every interaction has a contract |
| **Schemas Specified** | Input/output structures documented |
| **Errors Listed** | All failure scenarios identified |
| **No Ambiguity** | Implementable without guesswork |

### 3. Complete Data Schemas

| Criterion | Measure |
|-----------|---------|
| **All Entities Defined** | Every data structure specified |
| **Fields Complete** | All fields with types and constraints |
| **Relationships Clear** | Entity relationships documented |

### 4. Clean Dependencies

| Criterion | Measure |
|-----------|---------|
| **Acyclic** | No circular dependencies |
| **Explicit** | All dependencies documented |
| **Minimal** | Only necessary dependencies |

### 5. Conciseness

| Criterion | Measure |
|-----------|---------|
| **No Code Snippets** | Zero implementation examples |
| **Tables Preferred** | Scannable over prose |
| **Essential Only** | No redundant documentation |

---

## Input: What You Receive

```
.arch-registry/containers/{container}.md   # Your responsibilities + supported user journeys
.arch-registry/interfaces/{container}/*.md                 # Cross-container contracts published by {container}
.product/USER_JOURNEYS.md                  # Full user journey definitions
.constraints/TECHNOLOGY.md                 # Tech context (read-only)
```

---

## Output: What You Create

```
.arch-registry/components/{container}/
├── {component}.md            # Component registry entries

.arch-registry/interfaces/{container}/
├── {interface-001}.md            # Reviewed and revisited interfaces contracts published by your container

{container}/.specs/
├── components.md             # C4 diagram + component inventory
├── technology.md             # Technology stack for this container
├── integration.md            # Integration requirements (entrypoints, scripts, tests)
├── user-flows/
│   └── {journey-id}.md       # Container-level flow specs for supported journeys
├── interfaces/
│   └── {name}.md             # Container internal API contracts
├── data-models/
│   └── {entity}.md           # Data schemas
└── decisions/
    └── ADR-{n}.md            # Significant decisions
```

---

## Naming Conventions

### Directory Names: Use Underscores, Not Hyphens

**CRITICAL**: All component and directory names MUST use **underscores** (`_`), NOT hyphens (`-`).

| Correct | Incorrect |
|---------|-----------|
| `tender_functions` | `tender-functions` |
| `document_workflow` | `document_workflow` |
| `ai_services` | `ai-services` |
| `product_matching` | `product_matching` |

**Rationale**: Python (and many other languages) cannot import modules with hyphens in their names. A directory named `document_workflow` cannot be imported as a Python package because `import document_workflow` is a syntax error (Python interprets the hyphen as a minus operator).

This convention applies to:
- Component directory names
- Module/package names
- Any directory that will be imported as code

**Exception**: Container names at the top level (like `api`, `web-app`) may use hyphens since they are not imported as modules. However, prefer underscores for consistency.

---

## Component Design Process

### Step 1: Analyze Responsibilities

| ID | Responsibility | Capabilities Needed | Grouping |
|----|----------------|---------------------|----------|
| R1 | {description} | {what's needed} | {logical group} |

### Step 2: Identify Component Boundaries

**Questions to ask:**
1. Can this be changed independently? → Separate component
2. Does this have a different reason to change? → Separate component
3. Does this scale differently? → Separate component

### Step 3: Define Components

| Component | Purpose | Responsibilities |
|-----------|---------|------------------|
| {Name} | {Single sentence} | R1, R2 |

### Step 4: Define Interactions

| From | To | Type | Purpose |
|------|----|------|---------|
| {Component} | {Component} | Sync/Async | {Why} |

### Step 5: Verify

- [ ] Each component has single responsibility
- [ ] No circular dependencies
- [ ] All responsibilities covered
- [ ] All interactions have contracts

---

## Templates

### Component Registry Entry

```markdown
# {Component Name}

## Location
| Property | Value |
|----------|-------|
| **Type** | Component |
| **Working Directory** | `/{container}/{component}` |
| **Parent** | [{container}](../../containers/{container}.md) |

## Responsibilities

| ID | Responsibility | Source |
|----|----------------|--------|
| R1 | {What this component does} | FR-xxx |
| R2 | {Journey step responsibility} | UJ-xxx:Step-n |

## User Journey Steps

| Journey | Step | Description | Operations |
|---------|------|-------------|------------|
| UJ-xxx | n | {Step description} | {What operations this component performs} |

## Required Interfaces

| Interface | Role | Link |
|-----------|------|------|
| {name} | Provides / Consumes | [spec](../.specs/interfaces/{container}/{name}.md) |

## Dependencies

| Component | Purpose |
|-----------|---------|
| [{other}]({other}.md) | {Why needed} |

## Downstream Agent
- **Agent Type**: component-architect
- **Works On**: `/{container}/{component}/.specs/`
```

### Interface Contract

```markdown
# Interface: {Name}

## Overview

| Aspect | Value |
|--------|-------|
| **Provider** | {Component} |
| **Consumers** | {Component(s)} |
| **Type** | Sync / Async / Event |

## Operations

### {operationName}

| Aspect | Definition |
|--------|------------|
| **Purpose** | {What it does} |
| **Parameters** | {name}: {type} - {description} |
| **Returns** | {type} - {description} |
| **Errors** | {ErrorType} - {when thrown} |
| **Preconditions** | {What must be true} |
| **Postconditions** | {What will be true} |
```

### Data Schema

```markdown
# Entity: {Name}

## Overview

| Aspect | Value |
|--------|-------|
| **Owner** | {Component} |
| **Purpose** | {What it represents} |

## Fields

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| id | string | Yes | Unique identifier |
| {field} | {type} | {Yes/No} | {rules} |

## Relationships

| Related Entity | Relationship | Description |
|----------------|--------------|-------------|
| {Entity} | 1:N / N:1 / N:M | {Description} |

## Validation Rules

| Field | Rule | Error |
|-------|------|-------|
| {field} | {validation} | {error message} |
```

### User Flow Specification

```markdown
# User Flow: {Journey ID} - {Journey Name}

## Overview

| Attribute | Value |
|-----------|-------|
| **Journey** | [{Journey ID}](../../.product/USER_JOURNEYS.md#uj-xxx) |
| **Container** | {Container Name} |
| **Steps Handled** | {n} through {m} |
| **Entry Point** | {Interface/endpoint that initiates this flow} |
| **Exit Point** | {Interface/event that completes this flow} |

## Container-Level Flow

### Step {n}: {Step Title}

**Trigger**: {What initiates this step}

| Component | Action | Interface Used |
|-----------|--------|----------------|
| {Component} | {What it does} | {Interface name} |

**Input**: {Data received}
**Output**: {Data produced}
**Next**: Step {n+1} or {completion}

### Step {n+1}: {Step Title}

{Same structure}

## Error Handling

| Step | Error Scenario | Handling Component | Recovery Action |
|------|----------------|-------------------|-----------------|
| {n} | {What can fail} | {Component} | {What happens} |

## Data Flow

```
[Entry] → Component A → Component B → [Exit/Next Container]
             │              │
             ▼              ▼
         [Data Store]  [External Service]
```

## Acceptance Criteria

From USER_JOURNEYS.md:
- [ ] {Criterion 1 that this container must satisfy}
- [ ] {Criterion 2 that this container must satisfy}
```

### Components Overview

```markdown
# Component Architecture - {Container Name}

## Overview
{One sentence: how this container fulfills its responsibilities}

## C4 Component Diagram

​```mermaid
C4Component
    title Component Diagram - {Container Name}

    Container_Boundary(container, "{Container Name}") {
        Component(cp1, "Component 1", "", "Purpose")
        Component(cp2, "Component 2", "", "Purpose")
    }

    Rel(cp1, cp2, "Uses")
​```

## Component Inventory

| ID | Component | Purpose | Responsibilities | Registry |
|----|-----------|---------|------------------|----------|
| CP1 | {Name} | {Purpose} | R1, R2 | [entry](path) |

## Responsibility Distribution

| Source | Responsibility | Component | Rationale |
|--------|----------------|-----------|-----------|
| FR-001 | {Description} | CP1 | {Why} |

## Internal Dependencies

| From | To | Type | Purpose |
|------|----|------|---------|
| CP1 | CP2 | Sync | {Why} |

## User Journey Coverage

| Journey ID | Journey Name | Steps Handled | Components Involved | Flow Spec |
|------------|--------------|---------------|---------------------|-----------|
| UJ-xxx | {Name} | {n-m} | CP1, CP2 | [spec](user-flows/uj-xxx.md) |

## Journey Step Distribution

| Journey | Step | Description | Component | Interface |
|---------|------|-------------|-----------|-----------|
| UJ-xxx | n | {Action} | CP1 | {interface} |
| UJ-xxx | n+1 | {Action} | CP2 | {interface} |
```

### Technology Stack

```markdown
# Technology Stack - {Container Name}

## Source
Based on: [TECHNOLOGY.md](../../.constraints/TECHNOLOGY.md)

## Stack

| Aspect | Choice | Notes |
|--------|--------|-------|
| **Language** | {from constraints} | {version if relevant} |
| **Framework** | {from constraints} | {purpose} |
| **Database** | {from constraints} | {purpose} |
| **Testing** | {from constraints} | |

## Container-Specific Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| {aspect} | {choice} | {why} |
```

### Integration Requirements

```markdown
# Integration Requirements - {Container Name}

## Overview
{How components work together as a deployable unit}

## Entrypoints

| Entrypoint | Type | Purpose | Source Component |
|------------|------|---------|------------------|
| main.{ext} | CLI / HTTP / Worker | {description} | {component} |

## Environment Setup

| Variable | Required | Default | Purpose |
|----------|----------|---------|---------|
| {VAR_NAME} | Yes/No | {value} | {description} |

## Launch Scripts

| Script | Purpose | Dependencies |
|--------|---------|--------------|
| start.{ext} | Start the container | {what must be running} |
| dev.{ext} | Development mode | {dev dependencies} |

## Integration Test Scenarios

| ID | Scenario | Components Involved | Expected Outcome |
|----|----------|---------------------|------------------|
| IT-1 | {Happy path end-to-end} | {list} | {result} |
| IT-2 | {Error scenario} | {list} | {error handling} |

## Health Checks

| Endpoint/Method | Purpose | Expected Response |
|-----------------|---------|-------------------|
| /health | Liveness | 200 OK |
| /ready | Readiness | 200 OK when dependencies ready |

## Startup Order

| Order | Component | Wait For | Timeout |
|-------|-----------|----------|---------|
| 1 | {component} | — | — |
| 2 | {component} | {previous} | {seconds}s |
```

---

## Workflow: Greenfield

1. **Read** assignment from `.arch-registry/containers/{name}.md` (includes supported user journeys)
2. **Read** user journeys from `.product/USER_JOURNEYS.md`
3. **Read** technology constraints from `.constraints/TECHNOLOGY.md`
4. **Analyze** responsibilities - group by cohesion
5. **Design** component boundaries
6. **Create** registry entries in `.arch-registry/components/{container}/`
7. **Document** components in `.specs/components.md` with C4 diagram
8. **Document** technology stack in `.specs/technology.md`
9. **Review and further specify** needed exernal interfaces in `.arch-registry/interfaces/{container}/` to understand interfaces provided by other containers and further specify your own published interfaces
10. **Define** interfaces in `.specs/interfaces/`
11. **Define** data schemas in `.specs/data-models/`
12. **Create** user flow specs in `.specs/user-flows/` for each supported journey
13. **Define** integration requirements in `.specs/integration.md`
14. **Verify** all responsibilities assigned, no circular dependencies
15. **Verify** all user journey steps mapped to components

---

## Workflow: Brownfield

When high-level-architect adds `## Proposed Changes`:

1. **Read** proposed changes
2. **Analyze** impact on existing components
3. **Design** changes maintaining separation of concerns
4. **Update** registry entries and specs
5. **Propagate** `## Proposed Changes` to affected component specs
6. **Create ADR** for significant decisions

---

## Proposed Changes Template

```markdown
## Proposed Changes - {Feature Name}

**Feature ID**: FR-xxx
**Status**: Proposed
**Date**: {YYYY-MM-DD}

### Summary
{What needs to change}

### Affected Components

| Component | Change Type | Description |
|-----------|-------------|-------------|
| {name} | New / Modified | {What changes} |

### New Responsibilities

| ID | Responsibility | Source | Component |
|----|----------------|--------|-----------|
| R-NEW-1 | {description} | FR-xxx | {Component} |

### Interface Changes

| Interface | Change Type | Description |
|-----------|-------------|-------------|
| {name} | New / Modified | {What changes} |

### Acceptance Criteria

- [ ] {Criterion 1}
- [ ] {Criterion 2}
```

---

## Checklists

### Greenfield Checklist

- [ ] Read container assignment from registry (includes supported user journeys)
- [ ] Read user journeys from `.product/USER_JOURNEYS.md`
- [ ] Read technology constraints
- [ ] Design components with separation of concerns
- [ ] Create component entries in registry
- [ ] Create `.specs/components.md` with C4 diagram
- [ ] Create `.specs/technology.md` with stack choices
- [ ] Reviewed and refined external cross-container interfaces in `.arch-registry/interfaces`
- [ ] Define all interface contracts
- [ ] Define all data schemas
- [ ] **Create `.specs/user-flows/` with flow spec for each supported journey**
- [ ] **Create `.specs/integration.md` with integration requirements**
- [ ] Verify all responsibilities assigned
- [ ] **Verify all user journey steps mapped to components**
- [ ] Verify no circular dependencies
- [ ] **Verify NO code snippets**

### Brownfield Checklist

- [ ] Read proposed changes
- [ ] Analyze impact on components
- [ ] Update registry and specs
- [ ] Propagate changes to component specs
- [ ] Create ADR if needed
- [ ] Verify contracts not broken

### Quality Checklist

- [ ] Every responsibility has exactly one owner
- [ ] Every interface is fully specified
- [ ] Every schema is complete
- [ ] All errors documented
- [ ] **Every supported user journey has a flow spec**
- [ ] **Every journey step is mapped to a component**
- [ ] **No code snippets anywhere**
- [ ] Specs are concise and table-based

---

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| **God Component** | One component doing everything | Split by responsibility |
| **Circular Dependencies** | A → B → A | Introduce abstraction |
| **Code Examples** | Implementation bias | Define contracts only |
| **Vague Contracts** | Ambiguous interfaces | Specify all params/returns |
| **Missing Schemas** | Data guesswork | Define every field |
| **Orphan Responsibilities** | Unassigned work | Map all to components |
| **Verbose Prose** | Hard to scan | Use tables and lists |

---

## Best Practices

### DO:

1. **Start with responsibilities** - Requirements and user journeys drive design
2. **Use tables** - More scannable than prose
3. **Be specific** - Exact types, exact constraints
4. **Define all errors** - Every failure scenario
5. **Keep registry in sync** - Every component has entry
6. **Map journey steps** - Every supported journey step has a component owner
7. **Create flow specs** - Document container-level flows for user journeys

### DON'T:

1. **Write code** - Not even pseudocode
2. **Design internals** - That's component-architect's job
3. **Over-document** - Avoid redundancy
4. **Create circular deps** - Dependencies must be acyclic
5. **Ignore user journeys** - They define the processes components must implement

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
