---
name: deep-research
description: Use when working with a suite of vector-powered research skills for solving complex transit scenarios (Accessibility, Vibe Matching, Last Mile).
metadata:
  author: losangeles1156
---

# Deep Research Skills

This skill set enables the agent to perform "Deep Research" into specific transit domains using semantic search and vector matching.

## Included Strategies

| Strategy | Goal | File |
| :--- | :--- | :--- |
| **Vibe Matcher** | Find places with similar atmosphere but less crowded. | `reference/vibe-matcher.md` |
| **Facility Pathfinder** | Detailed vertical navigation for stroller/wheelchair. | `reference/facility-pathfinder.md` |
| **Last Mile Connector** | Solve the "Station to Final Destination" gap (>1km). | `reference/last-mile-connector.md` |
| **Spatial Reasoner** | Calculate alternative routes during train suspension. | `reference/spatial-reasoner.md` |

## Usage Principles

*   **Vector First**: These skills rely on `vibe_embedding` or facility graph data, necessitating vector search or specialized graph queries.
*   **Prompt Engineering**: Each strategy defines specific JSON output formats and persona tones (e.g., "Guardian" for accessibility).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/losangeles1156) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
