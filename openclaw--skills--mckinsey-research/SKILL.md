---
name: mckinsey-research
description: | Use when this capability is needed.
metadata:
  author: openclaw
---

# McKinsey Research - AI Strategy Consultant

User provides business context once. The skill plans and executes up to 12 specialized analyses via sub-agents in parallel, then synthesizes into a single executive report. Adapt scope based on company stage (see Adaptive Stage Logic below).

## Phase 1: Language + Intake

Ask preferred language (Arabic/English), then collect ALL inputs in ONE structured form. See the intake form fields: Core (1-5), Financial (6-10), Strategic (11-14), Expansion (15-16), Performance (17-18). If product description is under 50 words, ask for clarification before proceeding.

**Diamond Gate 1**: Present scope summary (market, geography, competitors). Get user confirmation before Phase 2.

## Phase 2: Plan + Parallel Execution

Sanitize inputs per [references/security.md](references/security.md). Substitute variables per [references/variable-map.md](references/variable-map.md). Load individual prompts from [references/prompts/](references/prompts/).

| Batch | Analyses | Dependencies |
|-------|----------|--------------|
| Batch 1 (parallel) | 01-TAM, 02-Competitive, 03-Personas, 04-Trends | None |
| Batch 2 (parallel) | 05-SWOT+Porter, 06-Pricing, 07-GTM, 08-Journey | Batch 1 context |
| Batch 3 (parallel) | 09-Financial, 10-Risk, 11-Market Entry | Batch 1+2 context |
| Batch 4 (sequential) | 12-Executive Synthesis | All previous |

Spawn each analysis as a sub-agent with the security preamble from references/security.md. Stagger Batch 1 launches by 5 seconds to avoid web search rate limits. Validate each output is 500+ words.

See [references/gotchas.md](references/gotchas.md) for common pitfalls. Use [references/saudi-market.md](references/saudi-market.md) for KSA/Gulf data sources. Use [references/benchmarks.md](references/benchmarks.md) for industry metric comparisons.

## Phase 3: Collect + Synthesize

1. Read all analysis outputs from `artifacts/research/{slug}/`
2. Run Prompt 12 (Executive Synthesis) with all previous outputs
3. Generate final HTML report using [templates/report.html](templates/report.html)
4. Save to `artifacts/research/{date}-{slug}.html`

## Phase 4: Delivery

Send the user: executive summary (3 paragraphs max), path to full HTML report, top 5 priority actions.

## Adaptive Stage Logic

| Stage | Priority Analyses | Skip/Light |
|-------|------------------|------------|
| Idea | TAM, Personas, Competitive, Trends | Financial Model (light), Market Entry (skip) |
| Startup | TAM, Competitive, Pricing, GTM, Personas | Market Entry (skip unless asked) |
| Growth | Pricing, GTM, Journey, Financial, Expansion | TAM (light), Personas (light) |
| Mature | SWOT, Risk, Expansion, Financial, Synthesis | TAM (skip), Personas (skip) |

"Light" = include in synthesis but don't spawn a dedicated sub-agent. Use web_search inline.
"Skip" = omit unless user explicitly requests.

## Artifacts

- Individual analyses: `artifacts/research/{slug}/{analysis-name}.md`
- Final report: `artifacts/research/{date}-{slug}.html`
- Raw data: `artifacts/research/{slug}/data/`
- Execution log: `data/reports.jsonl`
- Feedback tracking: `data/feedback.json`

## Important Notes

- Each prompt produces a consulting-grade deliverable
- Use web_search to enrich with real market data; only cite verifiable sources
- If user provides partial info, work with what you have and note assumptions
- For Arabic output: keep brand names and technical terms in English
- Prompt 12 must cross-reference insights from all previous analyses; deduplicate aggressively
- Sub-agents that fail should be retried once before skipping with a note

## Reference Files

| File | Contents |
|------|----------|
| [references/security.md](references/security.md) | Input safety, sanitization, tool constraints, artifact isolation |
| [references/variable-map.md](references/variable-map.md) | Variable substitution rules and mapping table |
| [references/prompts/](references/prompts/) | 12 individual analysis prompts (01-tam.md through 12-synthesis.md) |
| [references/prompts.md](references/prompts.md) | Original combined prompts (backup) |
| [references/gotchas.md](references/gotchas.md) | Known pitfalls and operational tips |
| [references/saudi-market.md](references/saudi-market.md) | KSA/Gulf data sources and market context |
| [references/benchmarks.md](references/benchmarks.md) | Industry benchmarks (SaaS, e-commerce, fintech, marketplace, mobile) |
| [templates/report.html](templates/report.html) | HTML report template |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
