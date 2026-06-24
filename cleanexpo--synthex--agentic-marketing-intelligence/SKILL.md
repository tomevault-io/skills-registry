---
name: agentic-marketing-intelligence
description: >- Use when this capability is needed.
metadata:
  author: CleanExpo
---

# Agentic Marketing Intelligence 2026

## Prime directive

Continuously improve portfolio websites using **real evidence, real data, and measurable
search-performance signals** — never invented metrics, never opinion treated as fact.

## Non-negotiable hard rules

1. **No fabricated data.** No invented rankings, impressions, CTR, volume, traffic, or conversions.
   Missing data is tagged `DATA_REQUIRED`. Unverifiable claims are `UNVERIFIED_CLAIM`. Opinion is
   `OPINION_SOURCE`. Promising-but-unproven is `HYPOTHESIS_FOR_TESTING`.
2. **Every recommendation ties to:** a source · a reason · a measurable signal · a proposed action · a
   validation method. (Enforced by `quality-gates.md`.)
3. **Confidence gates impact.** The master prioritiser is `(impact · confidence) / risk`. Influencer
   advice (`OPINION_SOURCE` = 0.1) can never out-rank a `CONFIRMED` action of similar impact.
4. **No live publishing without a human gate.** This skill prepares; humans approve. See
   `../../docs/marketing-intelligence/human-approval-gates.md`.
5. **Orchestrate, don't duplicate.** Local-pack/GBP/GSC mechanics belong to the existing skills; this
   skill consumes their output and adds cross-portfolio scoring + prioritisation.
6. **Internal first-party data overrides generic advice** whenever they conflict.

## What it does (one line each)

- **Ingest** Obsidian `Sources/` + supplied YouTube channels → source map.
- **Extract** claims/tactics/frameworks → claim dataset.
- **Verify** each claim against ≥4 references → confidence-rated ledger.
- **Score** pages on the 12 models in `scoring-models.ts`.
- **Prioritise** by confidence-adjusted action score; block high-risk / placeholder items.
- **Plan** human-gated tickets with rollback notes.
- **Loop** on daily/weekly/monthly cadences (`../../docs/marketing-intelligence/automation-schedule.md`).

## Files in this skill

| File | Purpose |
|------|---------|
| `skill.md` | This contract |
| `README.md` | Quick start + how it fits the ecosystem |
| `workflow.md` | The 7-agent run, step by step |
| `agent-prompts.md` | The prompt for each of the 7 agents |
| `quality-gates.md` | The 6 gates a run must pass |
| `scoring-models.ts` | The 12 formulas (pure, tested-shape, type-checked) |
| `types.ts` | Shared types incl. `ConfidenceLabel`, `DataStatus`, `Weights` |
| `inputs.schema.json` / `outputs.schema.json` | Run contract |
| `source-map.schema.json` / `claim-verification.schema.json` | Artifact contracts |
| `examples.md` | Worked example with placeholder→real transition |

## First-run status (2026-05-29)

Run #1 output is in `../../docs/marketing-intelligence/`. YouTube + live-metric deliverables are
`DATA_REQUIRED` because the vault contained no channel references and no GSC/Semrush exports were
present. The skill, schemas, math models, claim ledger, design docs and CEO synthesis are complete.

---
> Source: [CleanExpo/Synthex](https://github.com/CleanExpo/Synthex) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
