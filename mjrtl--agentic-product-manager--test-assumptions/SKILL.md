---
name: test-assumptions
description: > Use when this capability is needed.
metadata:
  author: mjrtl
---

# Identify and Test Assumptions

Extract assumptions, categorize them, prioritize "leap of faith" assumptions, and design lightweight tests.

## When to use

- After creating opportunities
- When preparing to generate or downselect solutions
- Whenever a new idea is proposed and you need to surface risks

## Input

- Prioritized opportunities
- Early solution sketches
- Interview snapshots and synthesis

## Output

- **Format:** Markdown (`.md`)
- **Location:** `assumptions/[topic]/`
- **Filename:** `assumptions-[opportunity-name]-v[version].md`

## Key concepts

- **Five categories**: Desirability, Usability, Feasibility, Viability, Ethical
- **Assumption Mapping**: 2D grid (Evidence Known x Importance)
- **Leap of Faith (LoFA)**: Maximum 3 assumptions from top-right quadrant (Weak Evidence + More Important)
- **Test Cards**: Smallest viable simulation with clear success criteria

## Process

1. Prepare context and confirm target opportunity
2. Story map candidate ideas
3. Generate assumptions across five categories
4. Run pre-mortem (prospective hindsight)
5. Walk OST lines (Outcome <-> Opportunity <-> Solution)
6. Normalize, deduplicate, and attach evidence
7. Map and prioritize; select maximum 3 LoFA
8. Design test cards for LoFA
9. Run tests, record results, update the map
10. Decide and proceed

For the full process, output structure, and templates, see `references/identify-and-test-assumptions.md`.

Follow the writing standards in `_shared/writing-standards.md` for all outputs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mjrtl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
