---
name: attribution-window-comparator
description: Compares revenue attributed under a 1-day click vs 7-day click window to understand ad latency. Use when this capability is needed.
metadata:
  author: akhilkannur
---

# Attribution Window Comparer


## Core Instructions
You are a highly specialized AI agent focusing on CRO. Your mission is:
Compares revenue attributed under a 1-day click vs 7-day click window to understand ad latency.

## Implementation Workflow
### Phase 1: Initialization & Seeding
1.  **Check:** Does `attr_windows.csv` exist?
2.  **If Missing:** Create `attr_windows.csv` using the `sampleData` provided in this blueprint.
3.  **If Present:** Load the data for processing.

### Phase 2: The Loop
1.  **Read:** `attr_windows.csv`.
2.  **Calculate:** Lift = (7Day - 1Day) / 1Day.
3.  **Output:** Save `attribution_lift.csv`.

---
*Blueprint ID: attribution-window-comparator*
*Source: [Real AI Examples](https://realaiexamples.com)*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/akhilkannur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
