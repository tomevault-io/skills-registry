---
name: ice-score
description: > Use when this capability is needed.
metadata:
  author: mjrtl
---

# ICE Score

Score an idea on Impact, Confidence, and Ease to propose execution priority.

## When to use

- To quickly order an idea backlog and select items for exploration
- When you have at least some explicit evidence and a rough effort estimate
- Before drafting experiment plans for a leaf opportunity

## Input

- **Idea title and description**: goal, target metric, scope, and working hypothesis
- **Idea analysis and current state**: data/user/market/test evidence, risks, and effort estimate
- Optional: target metric change (%), estimated effort (person-weeks)

## Output

- **Format:** Markdown (`.md`)
- **Location:** `initiatives/[initiative]/solutions/`
- **Filename:** `ice-[YYYY-MM-DD]-[slugified-idea-title].md`

## Scoring quick reference

- **Impact**: Map expected metric change (%) to 0-10 scale
- **Ease**: Map estimated effort (person-weeks) to 10-1 scale
- **Confidence**: Sum weighted evidence contributions with group caps
- **ICE Score** = Impact x Confidence x Ease

## Priority buckets

| Score | Guidance |
|---|---|
| >= 250 | Consider immediate execution (high expected ROI) |
| 150-249 | Promising; recommend additional precision testing |
| 100-149 | Proceed with mitigations or phase-two testing |
| < 100 | On hold or needs strengthening |

## Guardrails

- Do not invent or infer evidence
- Exclude evidence not directly tied to Impact from Confidence
- When uncertain, apply conservative caps
- Prevent duplicate counting of the same source

For the full scoring model, evidence types, confidence calculation, and report format, see `references/ice-scoring-model.md`.

Follow the writing standards in `_shared/writing-standards.md` for all outputs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mjrtl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
