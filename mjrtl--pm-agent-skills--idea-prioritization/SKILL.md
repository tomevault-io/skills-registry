---
name: idea-prioritization
description: > Use when this capability is needed.
metadata:
  author: mjrtl
---

# Idea Prioritization (ICE Framework)

## Goal

Ingest an idea description and current state, score it on **Impact, Confidence, and Ease**, and compute the **ICE Score = Impact x Confidence x Ease** to propose an execution priority. All evaluations must follow explicit criteria and rely only on stated evidence; no inference or guesswork.

## When to use

- To quickly order an idea backlog and select items for exploration and experiments
- When you have at least some explicit evidence (interviews/data/tests) and a rough team effort estimate
- Before drafting experiment plans for a leaf opportunity in an Opportunity Solution Tree

## Input

- **Idea title and description**: goal, target metric, scope, and working hypothesis
- **Idea analysis and current state**: data/user/market/test evidence, execution hypothesis, risks, and effort estimate
- Optional:
  - Target metric and expected change rate (%) or range
  - Estimated effort (person-weeks)

## Output

- **Format:** Markdown (`.md`)
- **Location:** `initiatives/[initiative]/solutions/`
- **Filename:** `ice-[YYYY-MM-DD]-[slugified-idea-title].md`

## Scoring model

For the full scoring model details, see `references/ice-scoring-model.md`.

### Quick reference

- **Impact**: Map expected metric change (%) to a 0-10 scale
- **Ease**: Map estimated effort (person-weeks) to a 10-1 scale (less effort = higher ease)
- **Confidence**: Sum weighted evidence contributions with group caps
- **ICE Score** = Impact x Confidence x Ease

### Priority buckets

| Score | Guidance |
|---|---|
| >= 250 | Consider immediate execution (high expected ROI) |
| 150-249 | Promising; recommend additional precision testing |
| 100-149 | Proceed with mitigations or phase-two testing |
| < 100 | On hold or needs strengthening |

## Process

1. **Input validation**: Verify target metric, expected change (%), evidence text, and estimated effort (person-weeks). If missing, ask clarifying questions.
2. **Impact scoring**: Map % change to the Impact table; if missing, apply default Impact 2.
3. **Ease scoring**: Map person-weeks to the Ease table; if uncertain, use conservative lower ease.
4. **Evidence extraction and classification**: Count only impact-related evidence from the input. Tally per type and apply group caps.
5. **Confidence calculation**: Sum per-type contributions, apply group caps, compute final Confidence (0-10).
6. **ICE computation**: Compute ICE = I x C x E; assign priority bucket.
7. **Report generation**: Include score table, calculation rationale, cap applications, risks/assumptions, and recommended next steps.

## Output format

```markdown
# ICE Evaluation -- [Idea Title]

## Overview
- **Idea:** [Title]
- **One-line Summary:** [Brief description]
- **Target Metric:** [Metric name]
- **Assumptions/Scope:** [Key assumptions]

## Score Summary
- **Impact:** [I] (basis: [expected % change or default rule])
- **Ease:** [E] (basis: [person-weeks])
- **Confidence:** [C] (see details in references/ice-scoring-model.md)

## ICE Calculation
- ICE = [I] x [C] x [E] = **[Score]**
- **Priority Guidance:** [Bucket label]

## Input Summary
- **Expected Metric Change:** [value/none -> default 1.5% applied]
- **Estimated Effort (person-weeks):** [value/uncertain]
- **Evidence Excerpts:**
  - [Excerpt 1; classified as: user/market/test/...]
  - [Excerpt 2; classified as: ...]

## Risks / Assumptions
- [Key risk]
- [Key uncertainty]

## Recommended Next Steps
- [Tests/data collection/research/prototype]
- Confidence Improvement Plan: [Which evidence to strengthen]

## Notes
- ICE is a fast comparison/sorting tool; final decisions must also consider strategy, market, and resources.
```

## Guardrails

- Do not invent or infer evidence or over-credit weak signals
- Exclude evidence not directly tied to Impact from Confidence
- When uncertain, apply conservative caps and document in "Risks/Assumptions"
- Prevent duplicate counting of the same source/content

## Customization (team tuning)

- Adjust Impact bands to your target metric sensitivity
- Adjust Ease bands to team speed/role mix
- Extend evidence keywords to your domain language, while preserving the "explicit evidence only" rule
- Recalibrate bucket thresholds per quarterly capacity/roadmap density

Follow the writing standards in `_shared/writing-standards.md` for all outputs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mjrtl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
