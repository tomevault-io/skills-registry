---
name: niche-success-scorecard
description: Quantitative scoring of niche viability for lifestyle SaaS ($1-3M ARR, solo/duo team). Use after completing a first-pass evaluation to generate a weighted score and go/no-go decision. Triggers on requests like "score this niche", "rate this opportunity", "should I pursue this", or "give me a scorecard for [niche]". Produces a 0-100 weighted score with clear decision thresholds. Use when this capability is needed.
metadata:
  author: kirillpolevoy
---

# Niche Success Scorecard — Lifestyle SaaS

## Goal

Generate a quantitative score (0–100) to determine if a niche can realistically reach $1–3M ARR with a solo/duo team.

## When to Use

Run this **after** completing a First Pass evaluation. Requires concrete data on distribution, pricing, competition, and buildability.

## Scoring System

Each dimension scored 0–5, then weighted. Total = 100 points max.

---

### 1. Distribution You Can Execute Solo (35%)

| Score | Criteria |
|-------|----------|
| 0 | No identifiable list, channel, or inbound path |
| 1 | Vague audience; would require paid ads with no proven playbook |
| 2 | Audience exists but no clean list; cold outbound only |
| 3 | One grindy channel that could work with sustained effort |
| 4 | Clear list OR strong inbound signal, but not both |
| 5 | Clean buyer list + at least one secondary channel/inbound confirmed |

**Weight: 35 points** (Score × 7)

---

### 2. ARPA + Path to $1–3M ARR (20%)

| Score | Criteria |
|-------|----------|
| 0 | Requires 5,000+ customers to hit $1M ARR |
| 1 | Requires 3,000–5,000 customers |
| 2 | Requires 2,000–3,000 customers |
| 3 | Requires 1,000–2,000 customers |
| 4 | Requires 500–1,000 customers |
| 5 | Requires 200–500 customers (ARPA $150–$400/mo) |

**Weight: 20 points** (Score × 4)

---

### 3. Willingness to Pay / Urgency (15%)

| Score | Criteria |
|-------|----------|
| 0 | Pure nice-to-have; no deadline or pain driver |
| 1 | Mild convenience; saves some time |
| 2 | Noticeable time saver; some frustration with status quo |
| 3 | Clear time/money saver; buyers actively looking for solutions |
| 4 | Compliance-adjacent or tied to revenue; strong motivation |
| 5 | Mandatory/deadline-driven; non-compliance = penalties or lost revenue |

**Weight: 15 points** (Score × 3)

---

### 4. Scope + Support Load (15%)

| Score | Criteria |
|-------|----------|
| 0 | Heavy integrations, multi-jurisdiction, or extreme edge cases |
| 1 | Significant complexity; would need contractor help |
| 2 | Buildable but will have annoying support burden |
| 3 | Buildable in 6–10 weeks; moderate support expected |
| 4 | Buildable in 4–6 weeks; support load manageable |
| 5 | Buildable in 2–4 weeks; low support; clear scope |

**Weight: 15 points** (Score × 3)

---

### 5. Expandability (10%)

| Score | Criteria |
|-------|----------|
| 0 | Dead-end; no adjacent markets or upsells |
| 1 | Theoretical expansion but unclear path |
| 2 | One adjacent market or upsell tier possible |
| 3 | 2–3 adjacent verticals or clear upsell path |
| 4 | Strong lateral expansion (device types, states, compliance areas) |
| 5 | Platform potential; multiple expansion vectors confirmed |

**Weight: 10 points** (Score × 2)

---

### 6. Competition / Crowding (5%)

| Score | Criteria |
|-------|----------|
| 0 | Funded incumbents dominate; no wedge visible |
| 1 | Strong incumbents with loyal customer base |
| 2 | Incumbents exist but stagnant or poorly executed |
| 3 | Incumbents exist but clear wedge opportunity |
| 4 | Few direct competitors; mostly generic substitutes |
| 5 | Greenfield; no credible niche-specific players |

**Weight: 5 points** (Score × 1)

---

## Decision Thresholds

| Score Range | Decision | Action |
|-------------|----------|--------|
| 80–100 | **Pursue aggressively** | Start validation immediately |
| 65–79 | **Pursue conditionally** | Must clear 1–2 specific risks first |
| 50–64 | **Hold** | Needs more research or market timing |
| < 50 | **Kill** | Move on; don't revisit without new signal |

---

## Bonus: Revenue Quality (Unweighted)

Not scored, but note for decision-making:

| Factor | Assessment |
|--------|------------|
| Contract structure | Monthly / Annual / Multi-year |
| Payment reliability | Prepaid / Net-30 / Invoice-heavy |
| Expansion revenue | Per-seat / Usage / Upsell tiers / None |

Annual contracts with expansion revenue = more defensible business.

---

## Output

**Filename:** `Niche_Score_<slug>.md`

```markdown
# Niche Success Score: <Title>

## Score Summary

| Metric | Value |
|--------|-------|
| **Total Score** | /100 |
| **Decision** | Pursue / Pursue-Conditional / Hold / Kill |
| **One-line rationale** | |

## Score Breakdown

| Dimension | Weight | Score (0–5) | Points | Notes |
|-----------|-------:|------------:|-------:|-------|
| Distribution (solo-executable) | 35 | | /35 | |
| ARPA + Path to $1–3M ARR | 20 | | /20 | |
| Willingness to Pay / Urgency | 15 | | /15 | |
| Scope + Support Load | 15 | | /15 | |
| Expandability | 10 | | /10 | |
| Competition / Crowding | 5 | | /5 | |
| **TOTAL** | 100 | — | **/100** | |

## Revenue Quality (Unweighted)

| Factor | Assessment |
|--------|------------|
| Contract structure | |
| Payment reliability | |
| Expansion revenue | |

## What Makes This Win (Top 3)

1. 
2. 
3. 

## What Kills This (Top 3)

1. 
2. 
3. 

## Lifestyle Math (Conservative)

| Metric | Value |
|--------|-------|
| Target ARPA | |
| Customers for $1M ARR | |
| Customers for $3M ARR | |
| Primary acquisition channel | |
| Biggest assumption | |

## Founder Fit Summary

| Question | Answer |
|----------|--------|
| Natural buyer access? | Yes / No / Partial |
| Enjoy 50+ sales calls? | Yes / No / Unsure |
| Care in 3 years? | Yes / No / Unsure |
| Ethical concerns? | None / Minor / Major |

## Defensibility Assessment

| Moat Type | Present? | Strength |
|-----------|----------|----------|
| Data/workflow lock-in | Yes/No | Weak / Moderate / Strong |
| Relationship moat | Yes/No | Weak / Moderate / Strong |
| Distribution exclusivity | Yes/No | Weak / Moderate / Strong |
| Speed moat | Yes/No | Weak / Moderate / Strong |

## Next Steps (2-Week Gate)

### Validation Interviews

- **Target:** X interviews with [buyer persona]
- **Must hear:** 
- **Must NOT hear:**

### Pilot Design

- **Concierge pilot:**
- **Pricing test:**
- **Success metric:**

### Go/No-Go Criteria

- **Proceed if:**
- **Stop if:**

### Day-by-Day Plan

| Day | Activity | Deliverable |
|-----|----------|-------------|
| 1–3 | | |
| 4–7 | | |
| 8–10 | | |
| 11–14 | | |
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kirillpolevoy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
