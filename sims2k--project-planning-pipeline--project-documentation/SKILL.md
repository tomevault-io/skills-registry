---
name: project-documentation
description: Create and manage structured project documentation with numbered folders, business canvases, validation workflows, and lifecycle tracking. Use when creating new projects, project notes, templates, business canvases, or when the user mentions project structure, PRDs, Lean Canvas, personas, experiments, or validation. Use when this capability is needed.
metadata:
  author: sims2k
---

# Project Documentation Skill

This skill enables skills-compatible agents to create and manage structured project documentation following validation-first methodologies, including business strategy canvases, lifecycle tracking, and numbered folder organization.

## Overview

This documentation system combines:
- **Zettelkasten principles** for connected knowledge
- **Lean startup methodology** for validation-first development
- **Numbered folder structure** for lifecycle navigation
- **Business canvases** for strategic planning

---

## Project Structure

Each project uses numbered folders aligned with the product lifecycle:

```
Projects/
  {{Project-Name}}/
    {{Project-Name}} MOC.md           # Central hub (Map of Content)
    
    00_Status & Roadmap/              # Dashboards, roadmaps, decisions
    01_Market Analysis/               # TAM/SAM/SOM, competitors, SWOT
    02_User Research/                 # Personas, empathy maps, interviews
    03_Product/                       # PRD, canvases, MVP spec
    04_Design/                        # UX flows, wireframes, copy
    05_Technical/                     # Tech stack, architecture, deploy
    06_Engineering/                   # Backlog, sprints, validation
    07_Analytics & Growth/            # KPIs, experiments, channels
    08_Legal & Privacy/               # GDPR, privacy policy
    09_Assets/                        # Images, diagrams, files
    Archive/                          # Completed/deprecated notes
```

### Stage Purpose Guide

| Stage | Folder | Purpose | Key Deliverables |
|-------|--------|---------|------------------|
| 00 | Status & Roadmap | Project management | Dashboard, Roadmap, ADRs |
| 01 | Market Analysis | Market opportunity | TAM/SAM, Competitors, SWOT |
| 02 | User Research | User understanding | Personas, Empathy Maps |
| 03 | Product | Product definition | PRD, Business Canvases |
| 04 | Design | User experience | UX Flows, Wireframes |
| 05 | Technical | Architecture | Tech Stack, System Design |
| 06 | Engineering | Execution | Backlog, Sprints |
| 07 | Analytics | Measurement | KPIs, Experiments |
| 08 | Legal | Compliance | Privacy, Terms |

---

## Template Catalog

Templates are numbered by their primary stage.

### 00 — Status & Roadmap
| Template | File | Purpose |
|----------|------|---------|
| Project MOC | `00_Project MOC.md` | Central navigation hub |
| Status Dashboard | `00_Status Dashboard.md` | Phase tracking, metrics |
| Roadmap | `00_Roadmap.md` | Timeline, milestones |
| Decision Log | `00_Decision Log.md` | ADRs |

### 01 — Market Analysis
| Template | File | Purpose |
|----------|------|---------|
| Market Overview | `01_Market Overview.md` | TAM/SAM/SOM, hypotheses |
| Competitor Map | `01_Competitor Map.md` | Competitive landscape |
| SWOT Analysis | `01_SWOT Analysis.md` | Strategic assessment |

### 02 — User Research
| Template | File | Purpose |
|----------|------|---------|
| Persona | `02_Persona.md` | User profiles, JTBD |
| Empathy Map | `02_Empathy Map.md` | Deep user understanding |
| Interview Notes | `02_Interview Notes.md` | User interview docs |

### 03 — Product
| Template | File | Purpose |
|----------|------|---------|
| PRD | `03_PRD.md` | Product requirements |
| Lean Canvas | `03_Lean Canvas.md` | One-page business plan |
| Value Proposition Canvas | `03_Value Proposition Canvas.md` | Customer-product fit |
| Business Model Canvas | `03_Business Model Canvas.md` | 9-block business model |

### 04 — Design
| Template | File | Purpose |
|----------|------|---------|
| UX Flow | `04_UX Flow.md` | User journey |

### 05 — Technical
| Template | File | Purpose |
|----------|------|---------|
| Tech Stack | `05_Tech Stack.md` | Architecture, stack |

### 06 — Engineering
| Template | File | Purpose |
|----------|------|---------|
| Backlog | `06_Backlog.md` | Prioritized tasks |
| Sprint | `06_Sprint.md` | Time-boxed work |

### 07 — Analytics & Growth
| Template | File | Purpose |
|----------|------|---------|
| Experiment | `07_Experiment.md` | Validation tests |
| KPIs | `07_KPIs.md` | Success metrics |

### XX — General
| Template | File | Purpose |
|----------|------|---------|
| Meeting Note | `XX_Meeting Note.md` | Any meeting |

---

## Business Strategy Canvases

### Lean Canvas (Ash Maurya)
**When to use**: Early-stage validation, problem-solution fit
**Components**: Problem, Solution, UVP, Unfair Advantage, Customer Segments, Key Metrics, Channels, Cost Structure, Revenue Streams

```
┌──────────┬──────────┬──────────┬──────────┬──────────┐
│ PROBLEM  │ SOLUTION │ UVP      │ UNFAIR   │ SEGMENTS │
│          │          │          │ ADVANTAGE│          │
├──────────┴──────────┼──────────┼──────────┴──────────┤
│ KEY METRICS         │          │ CHANNELS            │
├─────────────────────┴──────────┴─────────────────────┤
│ COST STRUCTURE             │ REVENUE STREAMS        │
└────────────────────────────┴─────────────────────────┘
```

### Business Model Canvas (Strategyzer)
**When to use**: Established product, strategic planning
**Components**: Customer Segments, Value Propositions, Channels, Customer Relationships, Revenue Streams, Key Resources, Key Activities, Key Partnerships, Cost Structure

### Value Proposition Canvas (Strategyzer)
**When to use**: Deep customer-product fit analysis
**Components**: 
- **Customer Profile**: Jobs, Pains, Gains
- **Value Map**: Products, Pain Relievers, Gain Creators

### SWOT Analysis
**When to use**: Strategic assessment, competitive positioning
**Components**: Strengths, Weaknesses, Opportunities, Threats

### Empathy Map
**When to use**: Deep user understanding
**Components**: See, Hear, Think & Feel, Say, Do, Pains, Gains

---

## Validation Workflow

Projects progress through decision gates:

```
┌──────────────────────────────────────────────────────────────┐
│ PHASE 1: DISCOVER & VALIDATE (2-4 weeks)                     │
│ Deliverables: Market Overview, Personas, Landing Page        │
│ GATE: 200 signups OR ≥5% conversion + ≥20% WTP              │
├──────────────────────────────────────────────────────────────┤
│ PHASE 2: PROTOTYPE (4-8 weeks)                               │
│ Deliverables: Demo, A/B tested landing, email sequence       │
│ GATE: Engagement metrics sufficient                          │
├──────────────────────────────────────────────────────────────┤
│ PHASE 3: MVP (2-4 months)                                    │
│ Deliverables: Accounts, core features, payments              │
│ GATE: Paying users + positive unit economics                 │
├──────────────────────────────────────────────────────────────┤
│ PHASE 4: GROWTH (ongoing)                                    │
│ Deliverables: Mobile, integrations, growth loops             │
│ GATE: Sustainable growth                                     │
└──────────────────────────────────────────────────────────────┘
```

### Decision Framework

```
IF targets_met:
    → PROCEED to next phase
ELIF partial_success:
    → ITERATE: refine and re-test
ELSE:
    → PIVOT messaging/audience OR STOP
```

### Experiment Hypothesis Format

```markdown
> [!experiment] Hypothesis
> **IF** we [action],  
> **THEN** we expect [measurable outcome],  
> **BECAUSE** [reasoning/insight].
```

---

## Creating a New Project

### Step 1: Create Folder Structure

```
Projects/
  New-Project/
    New-Project MOC.md
    00_Status & Roadmap/
    01_Market Analysis/
    02_User Research/
    03_Product/
    04_Design/
    05_Technical/
    06_Engineering/
    07_Analytics & Growth/
    08_Legal & Privacy/
    09_Assets/
    Archive/
```

### Step 2: Create Project MOC

Use `00_Project MOC.md` template with:
- Business value hypothesis in `lead` field
- Quick navigation table to all stages
- Dataview queries for recent activity
- Key metrics table

### Step 3: Initialize Key Documents

**Minimum viable documentation**:
1. `00_Status & Roadmap/Status_Dashboard.md`
2. `01_Market Analysis/Market_Overview.md`
3. `02_User Research/Persona_Primary.md`
4. `03_Product/Lean_Canvas.md`

### Step 4: Create Supporting Canvases

Visual `.canvas` files for key workflows:
- `03_Product/Lean_Canvas.canvas`
- `04_Design/UX_Flow.canvas`
- `05_Technical/Architecture.canvas`
- `00_Status & Roadmap/Roadmap.canvas`

---

## Tag Taxonomy

### Type Tags
```
type/moc, type/dashboard, type/roadmap, type/decision, type/adr
type/analysis, type/market, type/competitive, type/swot
type/persona, type/empathy-map, type/interview, type/research
type/prd, type/product, type/canvas
type/lean-canvas, type/business-model, type/value-proposition
type/design, type/ux
type/technical, type/architecture
type/backlog, type/sprint, type/engineering
type/experiment, type/validation, type/metrics, type/kpi
type/meeting
```

### Stage Tags
```
stage/00-status, stage/01-market, stage/02-research
stage/03-product, stage/04-design, stage/05-technical
stage/06-engineering, stage/07-analytics, stage/08-legal
```

### Project Tags
```
project/{{project-name}}   # Lowercase, hyphenated
```

### Status Values
```
status: active | paused | completed | archived
```

### Phase Values
```
phase: discovery | validation | build | growth | scale
```

---

## Frontmatter Standard

```yaml
---
# ═══════════════════════════════════════════════════════════════════════════════
# TEMPLATE: [Template Name]
# STAGE: [00-08] - [Stage Name]
# PURPOSE: [One-line description]
# ═══════════════════════════════════════════════════════════════════════════════
tags:
  - type/[type]
  - project/[project-name]
  - stage/[00-08]-[stage]
aliases: []
cssclass: [class]
lead: "One-line summary for callouts"
banner: "![[banner.jpg]]"
icon: "🚀"
created: "YYYY-MM-DD"
modified: "YYYY-MM-DD"
template:
  name: "[Template Name]"
  version: "2.0"
  stage: "[00-08]"
project: "[Project Name]"
status: active
phase: discovery
priority: 3
---
```

---

## Back Matter Standard

```markdown
---
## Back Matter

**Source**:: [[Origin Note]]
**References**:: [[Related Note]]
**Used By**:: [[Consumer Note]]

---
**Open Questions**
- ❓ Question

**Action Items**
- [ ] Task
```

---

## Dataview Queries

### Recent Project Activity

````markdown
```dataview
TABLE type AS "Type", modified AS "Updated"
FROM "Projects/{{Project}}"
WHERE file.name != this.file.name
SORT modified DESC
LIMIT 10
```
````

### Open Tasks

````markdown
```dataview
TASK FROM "Projects/{{Project}}"
WHERE !completed
SORT priority ASC
LIMIT 10
```
````

### Experiments by Status

````markdown
```dataview
TABLE experiment_status AS "Status", result AS "Result"
FROM "Projects/{{Project}}"
WHERE contains(tags, "type/experiment")
SORT created DESC
```
````

### Decisions

````markdown
```dataview
TABLE adr_status AS "Status", decision_date AS "Date"
FROM "Projects/{{Project}}"
WHERE contains(tags, "type/decision")
SORT decision_date DESC
```
````

---

## Canvas Patterns

Use `.canvas` files for visual documentation:

| Canvas | Stage | Purpose |
|--------|-------|---------|
| `Roadmap.canvas` | 00 | Phase timeline |
| `Lean_Canvas.canvas` | 03 | Business plan |
| `Business_Model.canvas` | 03 | 9-block model |
| `Value_Proposition.canvas` | 03 | Customer-product fit |
| `UX_Flow.canvas` | 04 | User journey |
| `Architecture.canvas` | 05 | System design |

See **obsidian-json-canvas** skill for canvas structure details.

---

## References

- [Zettelkasten Starter Kit](https://github.com/groepl/Obsidian-Zettelkasten-Starter-Kit)
- [Lean Canvas](https://leanstack.com/)
- [Business Model Canvas](https://www.strategyzer.com/)
- [Value Proposition Canvas](https://www.strategyzer.com/)
- [JSON Canvas Spec](https://jsoncanvas.org/spec/1.0/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sims2k) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
