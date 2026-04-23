---
name: high-level-architecture
description: Documentation format, best practices, and guidelines for System Context and Container level architecture. Handles greenfield initialization, brownfield feature requests, and maintains the architecture registry as the central source of truth. Use when this capability is needed.
metadata:
  author: wtah
---

# High-Level Architecture Skill

This skill defines how the high-level architect **processes product requirements and technical constraints** into a distributed set of responsibilities across containers. The architect maintains `.arch-registry/` as the central source of truth and coordinates work across all downstream agents.

---

## Core Principle

**You design the "what" and "where", downstream agents design the "how".**

| Your Job | Not Your Job |
|----------|--------------|
| Define container boundaries | Design component internals |
| Assign responsibilities to containers | Write detailed interface specs |
| Map requirements to containers | Create class/module designs |
| Propose changes for features | Implement code |
| Coordinate cross-container interfaces | Detail deployment infrastructure |

---

## Input Sources

Always read these before architectural work:

### `.product/` - What to Build

```
.product/
├── SUMMARY.md              # Vision, problem, target users, scope
├── REQUIREMENTS.md         # FR-xxx functional requirements with priorities
├── USER_JOURNEYS.md        # UJ-xxx end-to-end user processes
└── AI_CAPABILITIES.md      # AI-xxx features and integrations
```

### `.constraints/` - Technical Boundaries

```
.constraints/
├── TECHNOLOGY.md           # Languages, frameworks, databases
├── INFRASTRUCTURE.md       # Cloud, compute, networking, CI/CD
└── INTEGRATIONS.md         # External systems to integrate
```

---

## Output: Architecture Registry

You **own and maintain** `.arch-registry/`:

```
.arch-registry/
├── README.md                    # Hierarchy tree + responsibility coverage + feature proposals
├── system.md                    # System Context level
├── containers/
│   └── {container-name}.md      # One file per container
├── components/
│   └── {container}/             # Placeholder directories for container-architect
├── deployment/
│   └── {environment}.md         # Coordination with deployment-architect
└── interfaces/
    └── {interface-name}.md      # Cross-container interface stubs
```

---

## Registry Templates

### System Entry (`system.md`)

```markdown
# {System Name}

## Location
| Property | Value |
|----------|-------|
| **Type** | System |
| **Working Directory** | `/` |

## Overview
{Brief description from .product/SUMMARY.md}

## C4 System Context Diagram

Maintain the System Context diagram showing the system and its external actors/systems:

​```mermaid
C4Context
    title System Context Diagram - {System Name}

    Person(user, "User", "Description")
    System(system, "{System Name}", "Description")
    System_Ext(external, "External System", "Description")

    Rel(user, system, "Uses")
    Rel(system, external, "Integrates with")
​```

## External Actors

| Actor | Type | Description |
|-------|------|-------------|
| {User/System} | Person / External System | {What they do} |

## C4 Container Diagram

Maintain the Container diagram showing internal containers and their relationships:

​```mermaid
C4Container
    title Container Diagram - {System Name}

    Person(user, "User", "Description")

    Container_Boundary(system, "{System Name}") {
        Container(frontend, "Frontend", "Angular", "User interface")
        Container(api, "API", "Python/Azure Functions", "REST API")
        Container(ai, "AI Services", "Python", "AI processing")
    }

    System_Ext(external, "External System", "Description")

    Rel(user, frontend, "Uses", "HTTPS")
    Rel(frontend, api, "Calls", "REST/JSON")
    Rel(api, ai, "Triggers", "Events")
    Rel(api, external, "Integrates", "API")
​```

## Containers

| Container | Purpose | Link |
|-----------|---------|------|
| {name} | {Brief purpose} | [spec](containers/{name}.md) |

## External Systems

| System | Purpose | Integration |
|--------|---------|-------------|
| {name} | {Why needed} | [interface](interfaces/{name}.md) |

## Technology Stack
From `.constraints/TECHNOLOGY.md`:
- **Frontend**: {tech}
- **Backend**: {tech}
- **Database**: {tech}
- **Cloud**: {from .constraints/INFRASTRUCTURE.md}
```

### Container Entry (`containers/{name}.md`)

```markdown
# {Container Name}

## Location
| Property | Value |
|----------|-------|
| **Type** | Container |
| **Working Directory** | `/{container}` |
| **Parent** | [system](../system.md) |

## Responsibilities

| ID | Responsibility | Source |
|----|----------------|--------|
| R1 | {What this container does} | FR-xxx |
| R2 | {Another responsibility} | AI-xxx |
| R3 | {NFR-related responsibility} | NFR-PERF |
| R4 | {Journey step responsibility} | UJ-xxx |

**Source Legend:**
- `FR-xxx` - Functional Requirement from `.product/REQUIREMENTS.md`
- `AI-xxx` - AI Capability from `.product/AI_CAPABILITIES.md`
- `NFR-xxx` - Non-Functional Requirement (PERF, AVAIL, SEC, SCALE)
- `UJ-xxx` - User Journey from `.product/USER_JOURNEYS.md`

## Supported User Journeys

| Journey ID | Journey Name | Steps Handled |
|------------|--------------|---------------|
| UJ-xxx | {Journey title} | Steps {n-m} |

## Required Interfaces

| Interface | Role | Link |
|-----------|------|------|
| {name} | Provides / Consumes | [spec](../interfaces/{name}.md) |

## Technology
From `.constraints/TECHNOLOGY.md`:
- **Runtime**: {e.g., Python 3.11+}
- **Framework**: {e.g., Azure Functions}
- **Key Libraries**: {relevant to this container}

## Downstream Agent
- **Agent Type**: container-architect
- **Works On**: `/{container}/.specs/`
- **Creates**: Component design, internal interfaces, data models

## Notes
{Additional context for the container-architect}
```

### Interface Entry (`interfaces/{name}.md`)

```markdown
# Interface: {Interface Name}

## Overview
| Property | Value |
|----------|-------|
| **Type** | REST API / Events / gRPC |
| **Owner** | [{container}](../containers/{container}.md) |

## Description
{What this interface provides and why it exists}

## Participants

| Element | Role | Usage |
|---------|------|-------|
| [{name}](../containers/{name}.md) | Provider / Consumer | {How it uses this} |

## Contract Summary
{High-level description - detailed spec in owner's .specs/interfaces/}

### Key Operations / Events

| Name | Description |
|------|-------------|
| {operation/event} | {Brief description} |

## Specification Location
Detailed spec: `/{owner-container}/.specs/interfaces/{name}.md`
```

### Registry Index (`README.md`)

```markdown
# Architecture Registry - {System Name}

## Hierarchy

```
{System Name}
├── [frontend](containers/frontend.md) → /frontend
├── [api](containers/api.md) → /api
├── [ai-services](containers/ai-services.md) → /ai-services
└── [orchestration](containers/orchestration.md) → /orchestration

Deployments:
├── [production](deployment/production.md)
└── [staging](deployment/staging.md)

Interfaces:
├── [tender-api](interfaces/tender-api.md)
└── [ai-events](interfaces/ai-events.md)
```

## Responsibility Coverage

| Source | Requirement | Assigned To | Status |
|--------|-------------|-------------|--------|
| FR-001 | Tender upload | [api](containers/api.md) | Covered |
| FR-002 | Requirement extraction | [ai-services](containers/ai-services.md) | Covered |
| AI-001 | Document Understanding | [ai-services](containers/ai-services.md) | Covered |
| UJ-001 | Process New Tender | api, ai-services, frontend | Covered |

**Legend:** Covered | Gap | Proposed

## User Journey Coverage

| Journey | Name | Steps | Containers Involved |
|---------|------|-------|---------------------|
| UJ-001 | Process New Tender | 7 | frontend, api, ai-services |
| UJ-002 | Generate Proposal | 7 | frontend, api, ai-services |

### Journey Step Distribution

| Journey | Step | Description | Container |
|---------|------|-------------|-----------|
| UJ-001 | 1 | Upload tender | [api](containers/api.md) |
| UJ-001 | 2-4 | Parse & extract | [ai-services](containers/ai-services.md) |
| UJ-001 | 5-7 | Review & confirm | [frontend](containers/frontend.md) |

## Active Feature Proposals

| Feature | ID | Status | Affected Elements |
|---------|-------|--------|-------------------|
| {Feature name} | FR-xxx | Proposed / Approved / In Progress | {containers} |
```

---

## Workflow: Greenfield (New System)

When no `.arch-registry/` exists:

### Step 1: Read All Inputs

```
Read:
├── .product/SUMMARY.md          → Vision, scope, users
├── .product/REQUIREMENTS.md     → All FR-xxx requirements
├── .product/USER_JOURNEYS.md    → All UJ-xxx user journeys
├── .product/AI_CAPABILITIES.md  → All AI-xxx capabilities
├── .constraints/TECHNOLOGY.md   → Tech stack constraints
├── .constraints/INFRASTRUCTURE.md → Cloud/hosting constraints
└── .constraints/INTEGRATIONS.md → External system requirements
```

### Step 2: Initialize Registry Structure

```bash
mkdir -p .arch-registry/{containers,components,deployment,interfaces}
```

### Step 3: Design Container Boundaries

Determine containers based on:
- **Functional boundaries** - Group related requirements
- **Technology boundaries** - Different tech stacks need separation
- **Team boundaries** - Organizational structure
- **Scalability boundaries** - Different scaling needs

### Step 4: Create Registry Entries

1. Create `system.md` with overall context
2. Create `containers/{name}.md` for each container
3. Create `interfaces/{name}.md` for cross-container communication
4. Create `deployment/{env}.md` placeholders

### Step 5: Map All Requirements and Journeys

Ensure every FR-xxx, AI-xxx, and UJ-xxx has assigned containers:

```markdown
## Responsibility Coverage

| Source | Requirement | Assigned To | Status |
|--------|-------------|-------------|--------|
| FR-001 | ... | [container] | Covered |
| AI-001 | ... | [container] | Covered |
| UJ-001 | {Journey name} | [containers handling steps] | Covered |
```

### Step 5b: Map User Journey Steps to Containers

For each user journey, document which container handles which steps:

```markdown
## User Journey Coverage

| Journey | Step | Description | Container | Interface Used |
|---------|------|-------------|-----------|----------------|
| UJ-001 | 1 | Upload document | [api] | document-upload |
| UJ-001 | 2 | Parse document | [ai-services] | document-parser |
| UJ-001 | 3 | Review results | [frontend] | tender-api |
```

### Step 6: Create Container Spec Directories

For each container:

```bash
mkdir -p {container}/.specs/{interfaces,data-models,decisions,internal-flows}
```

Create `{container}/.specs/README.md` placeholder for container-architect.

### Step 7: Build Hierarchy Index

Update `README.md` with complete hierarchy tree.

---

## Workflow: Brownfield (Feature Request)

When processing a new requirement for an existing system:

### Step 1: Receive Feature Request

New requirement added to `.product/REQUIREMENTS.md` (e.g., FR-025).

### Step 2: Read Current State

```
Read:
├── .arch-registry/README.md     → Current hierarchy and coverage
├── .arch-registry/containers/*  → Existing container responsibilities
└── .arch-registry/interfaces/*  → Existing interfaces
```

### Step 3: Impact Analysis

Determine which containers are affected:

```markdown
## Impact Analysis - FR-025: {Feature Name}

### Affected Containers

| Container | Impact Level | Changes Required |
|-----------|--------------|------------------|
| api | Modified | New endpoint for X |
| ai-services | Major | New capability for Y |
| frontend | Modified | New UI for Z |
| orchestration | None | No changes |
```

### Step 4: Propose Changes

For each affected container, append `## Proposed Changes` to their spec files:

**Location**: `{container}/.specs/components.md` (or `README.md` if components.md doesn't exist)

```markdown
---

## Proposed Changes - {Feature Name}

**Feature ID**: FR-025
**Status**: Proposed
**Proposed By**: high-level-architect
**Date**: {YYYY-MM-DD}

### Summary
{What this feature requires from this container}

### Proposed Modifications

| Area | Current State | Proposed Change |
|------|---------------|-----------------|
| {Responsibility/Interface} | {What exists} | {What changes} |

### New Responsibilities

| ID | Responsibility | Source |
|----|----------------|--------|
| R-NEW-1 | {New responsibility} | FR-025 |

### Interface Changes

| Interface | Change Type | Description |
|-----------|-------------|-------------|
| {name} | New / Modified / Deprecated | {What changes} |

### Dependencies

- **Requires**: [{other-container}](path) to {provide something}
- **Blocks**: [{downstream}](path) until {condition}

### Risks & Considerations

- {Risk 1}
- {Risk 2}

### Acceptance Criteria

- [ ] {Criterion 1}
- [ ] {Criterion 2}
```

### Step 5: Update Registry

In `.arch-registry/README.md`:

1. Add to **Responsibility Coverage** with status "Proposed":
   ```markdown
   | FR-025 | {Description} | [ai-services](containers/ai-services.md) | Proposed |
   ```

2. Add to **Active Feature Proposals**:
   ```markdown
   | {Feature Name} | FR-025 | Proposed | api, ai-services, frontend |
   ```

### Step 6: Create New Interfaces (if needed)

If the feature requires new cross-container communication:
- Create `interfaces/{new-interface}.md`
- Reference from affected container entries

### Step 7: Await Approval

Proposals require stakeholder review before downstream agents act.

---

## Feature Proposal Lifecycle

```
1. Proposed    → High-level architect added proposed changes
2. Approved    → Stakeholders approved, ready for detailed design
3. In Progress → Downstream agents implementing detailed design
4. Implemented → Changes merged into main specs
5. Rejected    → Feature rejected, proposal removed
```

### Merging Approved Changes

Once a feature is fully implemented:

1. Move new responsibilities from `## Proposed Changes` into main `## Responsibilities`
2. Update container entries in `.arch-registry/containers/`
3. Remove the `## Proposed Changes - {Feature Name}` section
4. Update `README.md` to mark feature as "Implemented" then remove

---

## Delegation to Downstream Agents

### To Container-Architect

**You provide:**
- Container entry in `.arch-registry/containers/{name}.md`
- Responsibility assignments (FR-xxx, AI-xxx, UJ-xxx mappings)
- Supported user journeys and which steps this container handles
- Required interfaces to implement
- Technology constraints
- `## Proposed Changes` sections for features

**They create:**
- `{container}/.specs/components.md` - C4 Component diagram
- `{container}/.specs/interfaces/` - Detailed API contracts
- `{container}/.specs/data-models/` - Entity definitions
- `{container}/.specs/user-flows/` - Container-level flow specifications for supported journeys

### To Deployment-Architect

**You provide:**
- System and container overview
- NFR requirements from `.product/REQUIREMENTS.md`
- Infrastructure constraints from `.constraints/INFRASTRUCTURE.md`
- Deployment entry placeholders

**They create:**
- `.specs/deployment/` - C4 Deployment diagrams
- Infrastructure specifications
- CI/CD pipeline design

### To Dynamic-Flow-Architect

**You provide:**
- Container boundaries and interfaces
- Cross-container integration requirements
- User journeys from `.product/USER_JOURNEYS.md`
- Journey step distribution across containers

**They create:**
- `.specs/flows/` - C4 Dynamic diagrams
- Sequence documentation based on user journeys
- Error handling flows for journey exception scenarios

---

## Best Practices

### DO:
1. **Trace everything** - Every responsibility links to FR-xxx, AI-xxx, UJ-xxx, or NFR
2. **Keep registry minimal** - Only responsibilities and interfaces, not detailed design
3. **Define interfaces early** - Cross-container dependencies must be visible
4. **Verify coverage** - All requirements and user journeys must have assigned owners
5. **Use proposed changes** - Never modify existing specs directly for brownfield
6. **Respect constraints** - Technology choices must align with `.constraints/`
7. **Map user journeys** - Document which containers handle which journey steps

### DON'T:
1. **Design component internals** - That's container-architect's job
2. **Create detailed specifications** - Keep registry entries concise
3. **Skip impact analysis** - Always analyze before proposing changes
4. **Leave orphan requirements** - Every requirement and journey needs an owner
5. **Modify specs directly** - Use `## Proposed Changes` for brownfield
6. **Forget AI capabilities** - They need responsibility assignments too
7. **Ignore user journeys** - They define end-to-end processes the system must support

---

## Checklists

### Greenfield Checklist

- [ ] Read `.product/SUMMARY.md` for vision and scope
- [ ] Read `.product/REQUIREMENTS.md` for all FR-xxx
- [ ] Read `.product/USER_JOURNEYS.md` for all UJ-xxx
- [ ] Read `.product/AI_CAPABILITIES.md` for all AI-xxx
- [ ] Read `.constraints/*` for technology boundaries
- [ ] Create `.arch-registry/` directory structure
- [ ] Create `system.md` with system context
- [ ] **Create C4 System Context diagram in `system.md`**
- [ ] **Create C4 Container diagram in `system.md`**
- [ ] Create `containers/{name}.md` for each container
- [ ] **Add Supported User Journeys to each container**
- [ ] Create `interfaces/{name}.md` for cross-container communication
- [ ] Create `deployment/{env}.md` placeholders
- [ ] Build hierarchy in `README.md`
- [ ] Map all requirements in Responsibility Coverage
- [ ] **Map all user journeys in User Journey Coverage**
- [ ] **Document journey step distribution across containers**
- [ ] Create `{container}/.specs/` directories
- [ ] Verify 100% requirement coverage
- [ ] **Verify 100% user journey coverage**

### Brownfield Checklist

- [ ] Read new requirement from `.product/REQUIREMENTS.md`
- [ ] Read current `.arch-registry/` state
- [ ] Perform impact analysis
- [ ] Create `## Proposed Changes` in affected container specs
- [ ] Add to Active Feature Proposals in `README.md`
- [ ] Add to Responsibility Coverage with "Proposed" status
- [ ] Create new interfaces if needed
- [ ] Document dependencies between proposed changes
- [ ] **Update C4 diagrams in `system.md` if container boundaries change**

### Pre-Delegation Checklist

Before handing off to a downstream agent:

- [ ] Container entry exists in `.arch-registry/containers/`
- [ ] Responsibilities mapped with source (FR/AI/NFR)
- [ ] Required interfaces listed
- [ ] Technology stack documented
- [ ] Working directory specified
- [ ] `{container}/.specs/` directory created
- [ ] If brownfield: `## Proposed Changes` sections added

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
