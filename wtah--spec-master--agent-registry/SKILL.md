---
name: agent-registry
description: Centralized C4 architecture registry for tracking systems, containers, components, and deployments with their responsibilities and interfaces. Use when outlining architecture structure or managing cross-component dependencies. Use when this capability is needed.
metadata:
  author: wtah
---

# Architecture Registry

A lightweight registry that maps C4 model elements (Systems, Containers, Components, Deployments) to their working directories, responsibilities, and required interfaces.

---

## Purpose

The registry enables the high-level architect to:
1. **Process product requirements and constraints** into distributed responsibilities
2. **Outline the architecture structure** without detailed design (detail handled by downstream agents)
3. **Link each element to its working directory** in the codebase
4. **Document responsibilities** with traceability to requirements (FR-xxx, AI-xxx, NFR)
5. **Define required interfaces** that must be accessible across elements
6. **Verify completeness** - ensure all requirements are assigned to elements

---

## Registry Structure

```
.arch-registry/
├── README.md                    # Registry index with hierarchy tree
│
├── system.md                    # System Context level
│
├── containers/
│   └── {container-name}.md      # One file per container
│
├── components/
│   └── {container-name}/{component}.md   # One file per component
│
├── deployment/
│   └── {environment}.md         # One file per deployment environment
│
└── interfaces/
    └── {container-name}         # Container publishing the interface
        └── {interface-name}.md      # Shared cross-container interface definitions
```

### Example: E-Commerce System

```
.arch-registry/
├── README.md
├── system.md
│
├── containers/
│   ├── web-app.md
│   ├── api-gateway.md
│   └── backend-services.md
│
├── components/
│   ├── backend-services/
│   │   ├── order-service.md
│   │   ├── payment-service.md
│   │   └── inventory-service.md
│   └── web-app/
│       ├── checkout-ui.md
│       └── product-catalog.md
│
├── deployment/
│   ├── production.md
│   └── staging.md
│
└── interfaces/
    └── backend-services/
        ├── order-events.md
        └── payment-api.md
```

---

## Registry Index (README.md)

```markdown
# Architecture Registry - {System Name}

## Hierarchy

```
{System Name}
├── [web-app](containers/web-app.md) → /frontend
│   ├── [checkout-ui](components/web-app/checkout-ui.md) → /frontend/checkout
│   └── [product-catalog](components/web-app/product-catalog.md) → /frontend/catalog
│
├── [api-gateway](containers/api-gateway.md) → /gateway
│
└── [backend-services](containers/backend-services.md) → /services
    ├── [order-service](components/backend-services/order-service.md) → /services/orders
    ├── [payment-service](components/backend-services/payment-service.md) → /services/payments
    └── [inventory-service](components/backend-services/inventory-service.md) → /services/inventory

Deployments:
├── [production](deployment/production.md)
└── [staging](deployment/staging.md)

Shared Interfaces:
├── [order-events](interfaces/backend-services/order-events.md)
└── [payment-api](interfaces/backend-services/payment-api.md)
```

## Responsibility Coverage

This section tracks that all requirements are assigned to elements:

| Source | Requirement | Assigned To | Status |
|--------|-------------|-------------|--------|
| FR-001 | User authentication | [api-gateway](containers/api-gateway.md) | Covered |
| FR-002 | Order management | [order-service](components/backend-services/order-service.md) | Covered |
| AI-001 | Product recommendations | [product-catalog](components/web-app/product-catalog.md) | Covered |
| NFR-AVAIL | 99.9% uptime | [production](deployment/production.md) | Covered |
| UJ-001 | Document Upload | [web-app](containers/web-app.md) & [api-gateway](containers/api-gateway.md)  | Covered |

**Legend:** Covered = responsibility assigned | Gap = needs assignment | Partial = partially covered
```

---

## Element Documentation Template

Each element (system, container, component, deployment) uses the same template:

```markdown
# {Element Name}

## Location
| Property | Value |
|----------|-------|
| **Type** | System / Container / Component / Deployment |
| **Working Directory** | `{path-to-directory}` |
| **Parent** | [{parent-name}]({parent-path}.md) or `—` |

## Responsibilities

| ID | Responsibility | Source |
|----|----------------|--------|
| R1 | {Primary responsibility} | FR-001 |
| R2 | {Secondary responsibility} | AI-001 |
| R3 | {Additional responsibility} | NFR-PERF |
| R4 | {Additional responsibility} | UJ-003 |

**Source Types:**
- `FR-xxx` - Functional Requirement from `.product/REQUIREMENTS.md`
- `AI-xxx` - AI Capability from `.product/AI_CAPABILITIES.md`
- `NFR-xxx` - Non-Functional Requirement (PERF, AVAIL, SEC, SCALE)
- `UJ-xxx` - User Journey responsibility `.product/USER_JOURNEYS.md`
- `SUMMARY` - From `.product/SUMMARY.md` vision/scope

## Required Interfaces

Interfaces this element needs to consume or provide for cross-container coordination:

| Interface | Role | Link |
|-----------|------|------|
| {interface-name} | Consumes / Provides | [spec](.arch-registry/interfaces/{container}/{name}.md) |

## Notes

{Optional: Technology hints, constraints, or context for downstream agents}
```

---

## Interface Documentation Template

Interfaces define contracts that multiple elements need to understand:

```markdown
# Interface: {Interface Name}

## Overview
| Property | Value |
|----------|-------|
| **Type** | REST API / Events / Shared Types / gRPC / etc. |
| **Owner** | [{element-name}]({element-path}.md) |

## Description

{What this interface provides and why it exists}

## Consumers

| Element | Usage |
|---------|-------|
| [{name}]({path}.md) | {How it uses this interface} |

## Contract Summary

{High-level description of the contract}

### Key Operations / Events

| Name | Description |
|------|-------------|
| {operation/event} | {Brief description} |

## Specification Details

Detailed specification
```

---

## Workflow

### 1. High-Level Architect Creates Outline

The high-level architect sketches the architecture:

1. Create `.arch-registry/` directory
2. Define system in `system.md`
3. List containers in `containers/`
4. Identify key components in `components/`
5. Note and initialize required cross-container interfaces in `interfaces/{container}/{interface-name}.md` where {container} is the container publishing the interface
6. Build hierarchy in `README.md`

### 2. Downstream Agents Handle Detail

Each element's detailed architecture is handled by specialized agents:
- **Container architects** design components within their container
- **Component architects** design internal structure
- **Deployment architects** detail infrastructure and deployment

The registry only captures **what exists** and **what it's responsible for** includin g cross-container interfaces.

### 3. Interface Definitions Enable Coordination

When a component needs to interact with another:
1. Check `interfaces/` for existing contracts
2. If missing, create interface stub in registry
3. Owning element's agent provides detailed spec

---

## Quick Reference

### Create New Element

```bash
# Container
echo "# {Name}" > .arch-registry/containers/{name}.md

# Component
mkdir -p .arch-registry/components/{container}
echo "# {Name}" > .arch-registry/components/{container}/{name}.md

# Deployment
echo "# {Name}" > .arch-registry/deployment/{environment}.md

# Interface
echo "# Interface: {Name}" > .arch-registry/interfaces/{container}/{name}.md
```

### Update Hierarchy

After adding elements, update `.arch-registry/README.md` to reflect the new structure.

---

## Feature Request Workflow

When a new feature request comes in for an existing system (brownfield), the high-level architect analyzes the impact and proposes changes across affected components.

### Process

1. **Receive Feature Request** - New requirement documented in `.product/REQUIREMENTS.md` or new AI Capability or User-Journey
2. **Impact Analysis** - High-level architect identifies affected containers/components
3. **Propose Changes** - Add `## Proposed Changes - {Feature Name}` section to each affected element's `.specs/`
4. **Review & Approve** - Stakeholders review proposed changes
5. **Delegate to Downstream Agents** - Once approved, downstream agents implement detailed design

### Proposed Changes Section Template

Add this section at the **end** of each affected element's spec file (e.g., `{container}/.specs/components.md` or `{component}/.specs/design.md`):

```markdown
---

## Proposed Changes - {Feature Name}

**Feature ID**: FR-xxx
**Status**: Proposed | Approved | Rejected | Implemented
**Proposed By**: high-level-architect
**Date**: {YYYY-MM-DD}

### Summary

{Brief description of what this feature requires from this component}

### Proposed Modifications

| Area | Current State | Proposed Change |
|------|---------------|-----------------|
| {Responsibility/Interface/Data} | {What exists} | {What changes} |

### New Responsibilities

| ID | Responsibility | Source |
|----|----------------|--------|
| R-NEW-1 | {New responsibility} | FR-xxx |

### Interface Changes

| Interface | Change Type | Description |
|-----------|-------------|-------------|
| {name} | New / Modified / Deprecated | {What changes} |

### Dependencies

- **Requires**: [{other-component}]({path}) to {do something}
- **Blocks**: [{downstream-component}]({path}) until {condition}

### Risks & Considerations

- {Risk or consideration 1}
- {Risk or consideration 2}

### Acceptance Criteria

- [ ] {Criterion 1}
- [ ] {Criterion 2}
```

### Example: Adding Multi-Language Support

Feature Request: FR-025 - Support multiple languages for tender documents

**Affected Elements:**
- `ai-services/.specs/components.md` - New language detection component
- `api/.specs/components.md` - Modified document upload endpoint
- `frontend/.specs/components.md` - Language selector UI

Each affected spec file gets a `## Proposed Changes - Multi-Language Support` section appended.

### Tracking Feature Proposals

The registry README should track active feature proposals:

```markdown
## Active Feature Proposals

| Feature | ID | Status | Affected Elements |
|---------|-------|--------|-------------------|
| Multi-Language Support | FR-025 | Proposed | ai-services, api, frontend |
| CRM Integration | FR-030 | Approved | api, orchestration |
```

### Lifecycle

1. **Proposed** - High-level architect added proposed changes sections
2. **Approved** - Stakeholders approved, ready for detailed design
3. **In Progress** - Downstream agents working on detailed design
4. **Implemented** - Changes merged into main specs, proposed sections removed
5. **Rejected** - Feature rejected, proposed sections removed with rejection note

### Merging Approved Changes

Once a feature is fully implemented:
1. Move new responsibilities from "Proposed Changes" into main "Responsibilities" section
2. Update interfaces in the main spec
3. Remove the "Proposed Changes - {Feature Name}" section
4. Update registry README to mark feature as implemented

---

## Best Practices

1. **Keep entries minimal** - Only responsibilities and interfaces, not detailed design
2. **Link working directories** - Every element points to its code location
3. **Define interfaces early** - Cross-container dependencies should be visible upfront
4. **Let downstream agents detail** - The registry outlines; agents architect
5. **Use proposed changes for brownfield** - Never modify existing specs directly; propose first
6. **Track all active proposals** - Keep registry README updated with feature status

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
