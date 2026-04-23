---
name: product-requirements
description: Standardized structure for capturing product requirements, user needs, and AI capabilities. Input for architecture agents. Located in .product/ directory. Use when this capability is needed.
metadata:
  author: wtah
---

# Product Requirements Skill

This skill defines the **standardized structure** for documenting product requirements. The `.product/` directory serves as the primary input for architecture agents, capturing what needs to be built before any technical decisions are made.

---

## Core Concept

The `.product/` directory captures:
1. **What** the product does (not how)
2. **Who** uses it and their needs
3. **Why** it exists (business value)
4. **AI capabilities** that can enhance the product

This is **input** for the architecture process - it should be created before running `/init`.

---

## Directory Structure

```
.product/
├── SUMMARY.md                  # Executive summary of the product
├── REQUIREMENTS.md             # Detailed functional requirements
├── USER_JOURNEYS.md            # Target user journeys and processes
└── AI_CAPABILITIES.md          # AI/ML features and integrations
```

---

## File Templates

### `SUMMARY.md` - Executive Summary

```markdown
# Product Summary - {Product Name}

## Vision
{One sentence describing the ultimate goal of this product}

## Problem Statement
{What problem does this product solve? Who has this problem?}

## Solution Overview
{High-level description of how the product solves the problem}

## Target Users
| User Type | Description | Primary Need |
|-----------|-------------|--------------|
| {Type} | {Who they are} | {What they need} |

## Key Value Propositions
1. {Value proposition 1}
2. {Value proposition 2}
3. {Value proposition 3}

## Success Metrics
| Metric | Target | Measurement |
|--------|--------|-------------|
| {KPI} | {Target value} | {How measured} |

## Scope

### In Scope (MVP)
- {Feature/capability 1}
- {Feature/capability 2}

### Out of Scope (Future)
- {Deferred feature 1}
- {Deferred feature 2}

## Stakeholders
| Role | Name/Team | Interest |
|------|-----------|----------|
| Product Owner | {Name} | {Their focus} |
| Sponsor | {Name} | {Their interest} |

## Document History
| Date | Version | Author | Changes |
|------|---------|--------|---------|
| {Date} | 1.0 | {Author} | Initial version |
```

### `REQUIREMENTS.md` - Functional Requirements

```markdown
# Functional Requirements - {Product Name}

## Overview
This document captures all functional requirements for {Product Name}. Requirements are organized by domain and prioritized using MoSCoW (Must/Should/Could/Won't).

## Requirements Index

| ID | Requirement | Priority | Status |
|----|-------------|----------|--------|
| FR-001 | {Short description} | Must | Draft |
| FR-002 | {Short description} | Should | Approved |

---

## Domain: {Domain Name}

### FR-001: {Requirement Title}

**Priority**: Must | Should | Could | Won't

**Description**
{Detailed description of the requirement}

**User Story**
As a {user type}, I want {capability}, so that {benefit}.

**Acceptance Criteria**
- [ ] {Criterion 1}
- [ ] {Criterion 2}
- [ ] {Criterion 3}

**Business Rules**
- {Rule 1}
- {Rule 2}

**Dependencies**
- {Dependency on other requirements or systems}

---

### FR-002: {Requirement Title}

{Same structure as above}

---

## Non-Functional Requirements

### Performance
| Requirement | Target |
|-------------|--------|
| Response Time (p95) | {e.g., < 200ms} |
| Throughput | {e.g., 1000 req/s} |

### Security
| Requirement | Target |
|-------------|--------|
| Authentication | {e.g., OAuth 2.0} |
| Data Encryption | {e.g., AES-256 at rest} |

### Scalability
| Requirement | Target |
|-------------|--------|
| Concurrent Users | {e.g., 10,000} |
| Data Volume | {e.g., 1TB} |

---

## Glossary

| Term | Definition |
|------|------------|
| {Term} | {Definition} |

## Change Log

| Date | Requirement | Change | Author |
|------|-------------|--------|--------|
| {Date} | FR-001 | {What changed} | {Who} |
```

### `USER_JOURNEYS.md` - Target User Journeys

```markdown
# User Journeys - {Product Name}

## Overview
This document captures the key end-to-end user journeys that {Product Name} must support. User journeys describe the complete processes users perform to achieve their goals, showcasing what workflows the solution needs to enable.

## Journey Index

| ID | Journey | Primary Actor | Priority | Status |
|----|---------|---------------|----------|--------|
| UJ-001 | {Journey title} | {User type} | Must | Draft |
| UJ-002 | {Journey title} | {User type} | Should | Approved |

---

## UJ-001: {Journey Title}

### Overview
| Attribute | Value |
|-----------|-------|
| **Primary Actor** | {User type who initiates this journey} |
| **Goal** | {What the user wants to achieve} |
| **Priority** | Must / Should / Could |
| **Frequency** | {How often: Daily, Weekly, Per project, etc.} |
| **Estimated Duration** | {Typical time to complete} |

### Preconditions
- {What must be true before starting}
- {Required system state or user permissions}

### Trigger
{What initiates this journey - user action, system event, or time-based}

### Journey Steps

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         {Journey Title}                                  │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐          │
│  │  Step 1  │───▶│  Step 2  │───▶│  Step 3  │───▶│  Step 4  │          │
│  │ {Action} │    │ {Action} │    │ {Action} │    │ {Action} │          │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘          │
│       │                               │                                  │
│       ▼                               ▼                                  │
│  [Decision?]                    [Alternative]                           │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

| Step | Actor | Action | System Response | Notes |
|------|-------|--------|-----------------|-------|
| 1 | {Actor} | {What user does} | {What system does} | {Optional notes} |
| 2 | {Actor} | {What user does} | {What system does} | |
| 3 | {Actor} | {What user does} | {What system does} | |

### Alternative Flows

#### A1: {Alternative scenario name}
**Trigger**: {When this alternative occurs}
| Step | Action | Response |
|------|--------|----------|
| 2a | {Alternative action} | {Alternative response} |

### Exception Flows

#### E1: {Error scenario name}
**Trigger**: {What causes this error}
**Resolution**: {How it's handled}

### Postconditions
- {What is true after successful completion}
- {State changes in the system}

### Success Criteria
- [ ] {Measurable outcome 1}
- [ ] {Measurable outcome 2}

### Related Requirements
- FR-{XXX}: {Requirement title}
- FR-{XXX}: {Requirement title}

### UI/UX Considerations
- {Key screens or interactions}
- {Important usability considerations}

---

## UJ-002: {Another Journey Title}

{Same structure as above}

---

## Journey Map Summary

### By User Type

| User Type | Journeys | Primary Goals |
|-----------|----------|---------------|
| {Type 1} | UJ-001, UJ-003 | {Their main objectives} |
| {Type 2} | UJ-002, UJ-004 | {Their main objectives} |

### Journey Dependencies

```
UJ-001 ──────▶ UJ-002
   │              │
   │              ▼
   └────────▶ UJ-003 ──────▶ UJ-004
```

### Coverage Matrix

| Journey | {Feature 1} | {Feature 2} | {Feature 3} |
|---------|-------------|-------------|-------------|
| UJ-001  | ✓           | ✓           |             |
| UJ-002  |             | ✓           | ✓           |

---

## Glossary

| Term | Definition |
|------|------------|
| {Term} | {Definition in journey context} |

## Change Log

| Date | Journey | Change | Author |
|------|---------|--------|--------|
| {Date} | UJ-001 | Initial version | {Author} |
```

### `AI_CAPABILITIES.md` - AI/ML Features

```markdown
# AI Capabilities - {Product Name}

## Overview
This document captures AI/ML capabilities that enhance {Product Name}. These may be core features or augmentations to existing functionality.

## AI Strategy

### Vision
{How AI enhances the product value proposition}

### Principles
1. {Principle 1 - e.g., "AI assists, humans decide"}
2. {Principle 2 - e.g., "Transparency in AI decisions"}
3. {Principle 3 - e.g., "Privacy-first approach"}

---

## AI Capabilities Index

| ID | Capability | Type | Priority | Status |
|----|------------|------|----------|--------|
| AI-001 | {Name} | Core/Augment | Must | Planned |
| AI-002 | {Name} | Core/Augment | Should | Research |

---

## AI-001: {Capability Name}

### Classification
- **Type**: Core Feature | Augmentation | Automation
- **AI Category**: NLP | Computer Vision | Prediction | Recommendation | Generation
- **Priority**: Must | Should | Could

### Description
{What this AI capability does and why it matters}

### User Value
{How this benefits the user}

### Input/Output
| Input | Output |
|-------|--------|
| {Data type and source} | {What is produced} |

### Model Requirements
- **Type**: {Classification, Regression, Generative, etc.}
- **Training**: {Online, Offline, Pre-trained, Fine-tuned}
- **Accuracy Target**: {Minimum acceptable accuracy}

### Fallback Behavior
{What happens when AI is unavailable or uncertain}

---

## AI-002: {Another Capability}

{Same structure}

---

## Data Requirements

| Capability | Data Needed | Source | Volume |
|------------|-------------|--------|--------|
| AI-001 | {Data type} | {Source} | {Amount} |

## Vendor/Service Options

| Capability | Build vs Buy | Vendor Options |
|------------|--------------|----------------|
| AI-001 | {Recommendation} | {Options if buying} |

## Change Log

| Date | Change | Author |
|------|--------|--------|
| {Date} | Initial version | {Author} |
```

---

## Relationship to Architecture

The `.product/` directory provides **input** to the architecture process:

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│    .product/    │────▶│  Architecture   │────▶│ .agent-registry/│
│  (Requirements) │     │    Process      │     │    (Agents)     │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

### How Agents Use Product Requirements

| Agent | Uses From .product/ |
|-------|---------------------|
| `high-level-architect` | SUMMARY.md, REQUIREMENTS.md, USER_JOURNEYS.md for System Context |
| `container-architect` | Requirements and journeys for component design |
| `component-architect` | Detailed requirements for class design |
| `deployment-architect` | NFRs from REQUIREMENTS.md for infrastructure |
| `dynamic-flow-architect` | USER_JOURNEYS.md for flow documentation and sequence design |

---

## Best Practices

### DO:
1. **Focus on WHAT, not HOW** - Requirements describe needs, not solutions
2. **Use measurable criteria** - "Fast" is vague; "< 200ms" is testable
3. **Prioritize ruthlessly** - MoSCoW helps focus effort
4. **Include acceptance criteria** - Every requirement needs tests

### DON'T:
1. Include technical solutions - That's for architecture
2. Use ambiguous language - Avoid "should be easy to use"
3. Skip NFRs - Performance, security, scalability matter
4. Over-specify - Leave room for design decisions

---

## Checklist: Product Requirements Readiness

Before running `/init`, ensure:

- [ ] SUMMARY.md completed with vision and scope
- [ ] REQUIREMENTS.md has prioritized functional requirements
- [ ] REQUIREMENTS.md has NFR targets (performance, security, scalability)
- [ ] USER_JOURNEYS.md documents key end-to-end user processes
- [ ] AI_CAPABILITIES.md documents any AI/ML features (or states none)
- [ ] Stakeholders have reviewed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
