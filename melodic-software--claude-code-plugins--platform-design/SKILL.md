---
name: platform-design
description: Design Internal Developer Platforms, self-service capabilities, and golden paths Use when this capability is needed.
metadata:
  author: melodic-software
---

# Platform Design Command

Get guidance on designing Internal Developer Platforms (IDPs) and improving developer experience.

## Usage

```text
/systems-design:platform-design [aspect]
```

## Arguments

- `aspect` (optional): Specific area to focus on
  - `portal` - Developer portal design (Backstage, Port)
  - `templates` - Service templates and software catalogs
  - `provisioning` - Self-service infrastructure provisioning
  - `golden-paths` - Standardized development workflows
  - If omitted: Provide comprehensive IDP guidance

## Examples

```text
/systems-design:platform-design
/systems-design:platform-design portal
/systems-design:platform-design golden-paths
```

## Workflow

1. **Understand Current State**
   - Search for existing platform components
   - Look for: Backstage configs, Terraform modules, CI/CD templates
   - Identify current developer workflows

2. **Spawn Platform Engineer Agent**
   Use the `platform-engineer` agent to analyze and design. The agent specializes in:
   - Internal Developer Platform architecture
   - Developer portal design
   - Golden paths and paved roads
   - Self-service infrastructure
   - Platform team operating models

3. **Present Design Guidance**
   Provide recommendations organized by:
   - **Current State Assessment**
   - **Recommended Architecture**
   - **Implementation Roadmap**
   - **Success Metrics**

## Output Format

```text
## Platform Design Report

### Current State
- Existing components: [list]
- Developer pain points: [identified or ask]
- Platform maturity: [Level 0-4]

### Recommended Architecture
- [Component diagram or description]
- Key components and their roles
- Integration points

### Golden Paths
1. [Path name] - [Description]
   - Templates needed
   - Guardrails to implement
   - Escape hatches

### Implementation Roadmap
- Phase 1: [Quick wins]
- Phase 2: [Core capabilities]
- Phase 3: [Advanced features]

### Success Metrics
- Developer productivity: [metric]
- Time to first deploy: [metric]
- Platform adoption: [metric]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
