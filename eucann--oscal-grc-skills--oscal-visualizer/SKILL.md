---
name: oscal-visualizer
description: Create visual diagrams and representations of OSCAL documents including control hierarchies, component relationships, implementation flows, and SSP overviews. Inspired by oscal-diagrams and community visualization tools. Use when this capability is needed.
metadata:
  author: eucann
---

# OSCAL Visualizer Skill

Create visual representations of OSCAL documents to help understand control structures, relationships, and compliance status.

## When to Use This Skill

Use this skill when you need to:
- Visualize control hierarchies and families
- Show component relationships
- Display implementation coverage
- Create SSP overview diagrams
- Generate assessment flow charts
- Produce compliance dashboards

---

## ✅ Data Source Principle

This skill creates visualizations **from documents you provide**. All diagram content reflects your OSCAL data — no compliance information is generated from training knowledge.

---

## Diagram Types

| Type | Purpose | Best For |
|------|---------|----------|
| Control Hierarchy | Show control families and relationships | Catalogs, profiles |
| Component Relationships | Map components to controls | Component definitions |
| Implementation Flow | Show how controls are implemented | SSPs |
| Profile Inheritance | Display profile layering | Profiles |
| SSP Overview | System security summary | SSPs |
| Assessment Flow | Assessment process visualization | SAP, SAR |

## Visualization Color Schemes

### Control Families
| Family | Color | Hex |
|--------|-------|-----|
| AC (Access Control) | Red | #FF6B6B |
| AU (Audit) | Teal | #4ECDC4 |
| CM (Config Mgmt) | Green | #96CEB4 |
| IA (Auth) | Purple | #DDA0DD |
| SC (Sys/Comm) | Med Purple | #9370DB |
| SI (Integrity) | Turquoise | #00CED1 |

### Implementation Status
| Status | Color | Symbol |
|--------|-------|--------|
| Implemented | Green | ✅ |
| Partial | Yellow | ⚠️ |
| Planned | Blue | 🔵 |
| Not Applicable | Gray | ➖ |
| Missing | Red | ❌ |

## How to Create Visualizations

### Control Hierarchy Diagram

For catalogs and profiles:

```
NIST 800-53 Rev 5
├── Access Control (AC) [20 controls]
│   ├── AC-1: Policy and Procedures
│   ├── AC-2: Account Management
│   │   ├── AC-2(1): Automated Management
│   │   ├── AC-2(2): Automated Temporary Accounts
│   │   └── AC-2(3): Disable Accounts
│   └── AC-3: Access Enforcement
│       └── AC-3(1): Restricted Access
├── Audit and Accountability (AU) [16 controls]
│   ├── AU-1: Policy and Procedures
│   └── AU-2: Event Logging
...
```

### Component Relationship Diagram

```
┌─────────────────────────────────────────────────┐
│                   SYSTEM                         │
├─────────────────────────────────────────────────┤
│                                                  │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐  │
│  │ Azure AD │────│ App Svc  │────│ Azure DB │  │
│  └────┬─────┘    └────┬─────┘    └────┬─────┘  │
│       │               │               │         │
│  ┌────┴─────┐    ┌────┴─────┐    ┌────┴─────┐  │
│  │ AC-2,IA-2│    │ SC-7,CM-6│    │ SC-28,AU-2│  │
│  │ IA-5,AU-2│    │ SI-3,SI-4│    │ AC-3,SC-8 │  │
│  └──────────┘    └──────────┘    └──────────┘  │
│                                                  │
└─────────────────────────────────────────────────┘
```

### Implementation Status Heatmap

```
CONTROL IMPLEMENTATION STATUS
=============================

     AC  AT  AU  CA  CM  CP  IA  IR  MA  MP  PE  PL  PM  PS  RA  SA  SC  SI
    ┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
Imp │███│███│███│███│███│░░░│███│███│███│███│░░░│███│███│███│███│███│███│███│
Par │░░░│░░░│░░░│░░░│░░░│███│░░░│░░░│░░░│░░░│███│░░░│░░░│░░░│░░░│░░░│░░░│░░░│
Pln │░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│
N/A │░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│░░░│
    └───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘

Legend: ███ = Present  ░░░ = None
```

### SSP Overview Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    SYSTEM SECURITY PLAN                      │
│                   [System Name v1.0.0]                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌─────────────┐      ┌─────────────┐      ┌─────────────┐  │
│  │  METADATA   │      │   PROFILE   │      │   SYSTEM    │  │
│  │             │      │   IMPORT    │      │   CHARS     │  │
│  │ FedRAMP Mod │──────│ NIST 800-53 │──────│ Cloud SaaS  │  │
│  │ v2024.01    │      │ Moderate    │      │ Boundary    │  │
│  └─────────────┘      └─────────────┘      └─────────────┘  │
│                              │                               │
│                              ▼                               │
│  ┌───────────────────────────────────────────────────────┐  │
│  │            CONTROL IMPLEMENTATION                      │  │
│  │                                                        │  │
│  │  Controls: 325     Implemented: 287 (88%)             │  │
│  │  Partial: 25       Planned: 10      N/A: 3            │  │
│  │                                                        │  │
│  │  ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐         │  │
│  │  │AC: 100%│ │AU: 95% │ │CM: 90% │ │SC: 85% │         │  │
│  │  └────────┘ └────────┘ └────────┘ └────────┘         │  │
│  └───────────────────────────────────────────────────────┘  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Profile Inheritance Diagram

```
                    ┌──────────────────┐
                    │   NIST 800-53    │
                    │   (Catalog)      │
                    │   1189 controls  │
                    └────────┬─────────┘
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
      ┌───────────┐  ┌───────────┐  ┌───────────┐
      │   LOW     │  │ MODERATE  │  │   HIGH    │
      │ Baseline  │  │ Baseline  │  │ Baseline  │
      │ 200 ctrls │  │ 325 ctrls │  │ 421 ctrls │
      └─────┬─────┘  └─────┬─────┘  └─────┬─────┘
            │              │              │
            ▼              ▼              ▼
      ┌───────────┐  ┌───────────┐  ┌───────────┐
      │ FedRAMP   │  │ FedRAMP   │  │ FedRAMP   │
      │ LOW       │  │ MODERATE  │  │ HIGH      │
      │ +tailored │  │ +tailored │  │ +tailored │
      └───────────┘  └───────────┘  └───────────┘
```

## Compliance Dashboard View

```
╔═══════════════════════════════════════════════════════════╗
║              COMPLIANCE DASHBOARD                          ║
╠═══════════════════════════════════════════════════════════╣
║                                                            ║
║  OVERALL COMPLIANCE          RISK LEVEL                   ║
║  ┌────────────────┐          ┌────────────────┐           ║
║  │      88%       │          │    MODERATE    │           ║
║  │   ████████░░   │          │    ▲▲▲░░       │           ║
║  └────────────────┘          └────────────────┘           ║
║                                                            ║
║  CONTROL STATUS                POA&M STATUS               ║
║  ┌──────────────────┐         ┌──────────────────┐        ║
║  │ ✅ Impl:    287  │         │ Open:        15  │        ║
║  │ ⚠️  Partial:  25  │         │ In Progress:  8  │        ║
║  │ 🔵 Planned:  10  │         │ Overdue:      3  │        ║
║  │ ➖ N/A:       3  │         │ Closed (30d): 12 │        ║
║  └──────────────────┘         └──────────────────┘        ║
║                                                            ║
║  FAMILY COVERAGE                                          ║
║  AC ████████████████████ 100%                             ║
║  AU ██████████████████░░  95%                             ║
║  CM ████████████████░░░░  90%                             ║
║  IA ██████████████████░░  95%                             ║
║  SC █████████████████░░░  85%                             ║
║                                                            ║
╚═══════════════════════════════════════════════════════════╝
```

## Output Formats

| Format | Use Case |
|--------|----------|
| ASCII | Terminal display, text reports |
| Mermaid | Documentation, GitHub |
| DOT/Graphviz | Complex relationships |
| SVG | Web display |
| Markdown tables | Documentation |

## Example Usage

When asked "Visualize the control coverage in this SSP":

1. Parse the SSP document
2. Extract control implementations
3. Group by family
4. Calculate percentages by status
5. Generate appropriate visualization
6. Include legend and summary statistics

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eucann) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
