---
name: ux-flow-diagram
description: [UI/UX] Visualizes user flows and screen transitions as ASCII diagrams. Represents navigation flows, user journeys, and screen-to-screen paths. Use when requesting 'flow diagram', 'user journey visualization', or 'navigation flow'. Use when this capability is needed.
metadata:
  author: cantagestudio
---

# UX Flow Diagram

A skill that visualizes user flows and screen transitions as ASCII diagrams.

## When to Use

- Documenting user journeys
- Designing navigation flows between screens
- Defining user flows per feature
- Representing conditional branching and exception handling flows

## Flow Diagram Symbols

### Basic Nodes
```
┌─────────┐
│ Screen  │     ← Screen/Page
└─────────┘

╔═════════╗
║ Screen  ║     ← Start/End screen (emphasis)
╚═════════╝

((Action))      ← User action
<Decision?>     ← Condition/Branch point
[Process]       ← System process
```

### Connection Lines
```
───→     Unidirectional flow
←──→     Bidirectional flow
- - →    Optional/conditional flow
═══→     Main flow (emphasis)
```

## Flow Patterns

### Linear Flow (Sequential)
```
╔═══════════╗    ┌───────────┐    ╔═══════════╗
║   Start   ║───→│  Step 1   │───→║    End    ║
╚═══════════╝    └───────────┘    ╚═══════════╝
```

### Branching Flow
```
                         Yes  ┌───────────┐
                    ┌────────→│  Path A   │────┐
┌───────────┐       │         └───────────┘    │    ┌───────────┐
│  Screen   │───→<Decision?>                   ├───→│   Result  │
└───────────┘       │         ┌───────────┐    │    └───────────┘
                    └────────→│  Path B   │────┘
                         No   └───────────┘
```

## Constraints

- Flows progress left-to-right, top-to-bottom
- Complex flows should be split into sub-flows
- All branch points need clear condition labels

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cantagestudio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
