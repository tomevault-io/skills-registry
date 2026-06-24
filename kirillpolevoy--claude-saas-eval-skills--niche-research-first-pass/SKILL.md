---
name: niche-research-first-pass
description: First-pass evaluation of niche SaaS ideas through a lifestyle business lens ($1-3M ARR, solo/duo team). Use when evaluating a new niche hypothesis, researching a vertical software opportunity, or validating whether a market is worth pursuing. Triggers on requests like "evaluate this niche", "is this a good market", "research this vertical", or "first pass on [industry] software idea". Distribution-first methodology—kills ideas without concrete go-to-market paths. Use when this capability is needed.
metadata:
  author: kirillpolevoy
---

# Niche Research — First Pass (Lifestyle SaaS Lens)

## Goal

Evaluate whether a niche can become a $1–3M ARR lifestyle business run solo or with 1 partner.

**Ideal characteristics:**
- Simple product scope (v1 in 2–6 weeks)
- Short sales cycle (< 30 days)
- Low support burden (< 5 hrs/week at scale)
- Concrete distribution (lists, channels, or high-intent inbound)
- Pricing that requires < 2,000 customers for $1M ARR

## Voice

State conclusions first, evidence second. If distribution is unclear after research, output `FAIL: Distribution unclear` and stop. No hedging with "could potentially" or "might be worth exploring." If data is missing, say "Unknown—requires primary research."

## Inputs

Only ask for missing inputs:
- **Niche hypothesis** (one sentence describing the market + problem)
- **Geography** (where to focus research—no default assumed)
- **Buyer** (if known)

## Non-Negotiable Gate

A niche only passes if it has **at least one concrete distribution surface**:

| Distribution Type | Example |
|-------------------|---------|
| Clean buyer list | Directory, licensing board, registry, permit database |
| Channel partner | Supplier, distributor, association, insurer, payments provider |
| High-intent inbound | Compliance forms, deadline-driven searches, "how to file X" |
| System-of-record hook | Integration with existing workflow software |

**If none exist after research: output `FAIL (distribution)` and stop.**

## Process

Execute in order. Stop early if distribution fails.

### 1. Define the Niche Precisely

- Who pays? Who uses daily?
- What mandatory artifact exists? (forms, submissions, certificates, logs, reports)
- What's the "money leak"? (lost renewals, rejected applications, admin hours, penalties, missed revenue)

### 2. Identify the Paperwork Wedge

- What exact document/packet/submission is unavoidable?
- What deadlines exist? (annual, quarterly, event-triggered)
- Who gets blamed when it's wrong or late?

### 3. Competitor & Crowding Check

- Direct niche tools (name, pricing, market position)
- Generic substitutes (PDFs, spreadsheets, Jobber, HubSpot, email)
- Government/industry portals acting as system-of-record
- Funded players in adjacent spaces

### 4. Distribution Map

**Must be concrete—no "we could try LinkedIn."**

- List sources (actual URLs or database names)
- 1–2 plausible channel partners
- Inbound keywords indicating urgency (with search volume if available)
- Integration hooks (what software do they already use?)

If unclear after research: `FAIL: Distribution unclear. Cannot proceed.`

### 5. Founder Fit Check

- Do you have natural access to these buyers? (former industry, network, geography)
- Would you enjoy 50+ sales calls with this persona?
- Is this a market you'd still care about in 3 years?
- Any ethical/personal hesitations?

*Note: This section requires founder input. Flag if not provided.*

### 6. Buildability & Support Load

- Can core v1 be built in 2–6 weeks by a solo dev?
- If regulated/integrated workflow: add 4–8 week buffer
- If jurisdiction sprawl (state-by-state rules): add research time per region
- Any liability or compliance certification required?
- Edge case density: low / medium / high

### 7. Pricing for Lifestyle Outcome

| Tier | ARPA | When Acceptable |
|------|------|-----------------|
| Preferred | $150–$500/mo | Default target |
| Acceptable | $80–$150/mo | Only if churn < 3%/mo AND distribution is cheap |
| Avoid | < $50/mo | Only if distribution is nearly free (viral, marketplace) |

### 8. Revenue Quality

- Contract structure: monthly / annual / multi-year
- Payment behavior: prepaid / net-30 / invoice-heavy industries
- Expansion path: per-seat / usage-based / upsell tiers

### 9. Market Size (Conservative)

- Count payers using best available proxy (licenses, permits, businesses)
- Calculate path to $1M ARR: (target ARPA) × (customers needed)
- Calculate path to $3M ARR: (target ARPA) × (customers needed)
- Note key assumptions

### 10. Defensibility Check

At least one should be present:
- **Data/workflow lock-in**: Does usage create switching cost?
- **Relationship moat**: Are buyers sticky once onboarded?
- **Distribution exclusivity**: Can you lock up a channel?
- **Speed moat**: Can you ship fast enough to stay ahead?

### 11. Verdict + Kill Criteria

- **Pursue**: Clear distribution + viable pricing + buildable scope
- **Hold**: Promising but missing one key signal
- **Kill**: No distribution OR requires > 3,000 customers OR unbuildable scope

Include:
- Must-have signals to proceed
- 7-day validation plan

---

## Output

**Filename:** `Niche_FirstPass_<slug>.md`

```markdown
# Niche First Pass: <Title>

## TL;DR Verdict

- **Verdict:** Pursue / Hold / Kill
- **Why:** (3 blunt bullets)

## The Wedge

(One sentence: sellable workflow + mandatory artifact + outcome)

## Customer Profile

| Role | Description |
|------|-------------|
| Paying customer | |
| Daily user | |
| Trigger moment | |

## Workflow Today (As-Is)

1. 
2. 
3. 

## Pain / Money Leak

- **Time waste:**
- **Errors/rejects:**
- **Compliance risk:**
- **Revenue leak:**

## Existing Solutions

### Direct Niche Tools
| Tool | Pricing | Notes |
|------|---------|-------|
| | | |

### Substitutes
- 

## Distribution (Must Be Specific)

- **List source:** (actual URL/database)
- **Channel candidate:**
- **Inbound keywords:** (with volume if known)
- **Integration hook:**
- **Distribution risk:**

## Founder Fit

- [ ] Natural access to buyers
- [ ] Would enjoy 50+ sales calls with this persona
- [ ] Would care about this market in 3 years
- [ ] No ethical/personal hesitations

*Fit score: Strong / Moderate / Weak / Unknown*

## Buildability

| Factor | Assessment |
|--------|------------|
| Core v1 timeline | 2–6 weeks / 6–12 weeks / 12+ weeks |
| Regulatory buffer needed | Yes / No |
| Jurisdiction sprawl | None / Moderate / Severe |
| Edge case density | Low / Medium / High |
| Support load estimate | < 5 hrs/wk / 5–15 hrs/wk / 15+ hrs/wk |

### MVP Scope

**Must-have:**
- 

**Nice-to-have:**
- 

**Explicit non-goals:**
- 

## Pricing

- **Recommended tiers:**
- **Expected ARPA:**
- **Why it's fair:**

## Revenue Quality

- **Contract structure:**
- **Payment behavior:**
- **Expansion path:**

## Market Size (Conservative)

- **Payers in target geography:**
- **Total US payers:**
- **Path to $1M ARR:** X customers × $Y ARPA
- **Path to $3M ARR:** X customers × $Y ARPA
- **Key assumption:**

## Defensibility

| Moat Type | Present? | Notes |
|-----------|----------|-------|
| Data/workflow lock-in | | |
| Relationship moat | | |
| Distribution exclusivity | | |
| Speed moat | | |

## Biggest Entry Risks

1. 
2. 
3. 

## Kill Criteria

- If we don't hear X in 10 calls → kill
- If pricing won't clear $Y → kill
- If compliance/portal makes it redundant → kill

## 7-Day Validation Plan

| Day | Activity | Success Signal |
|-----|----------|----------------|
| 1 | | |
| 2–3 | | |
| 4–5 | | |
| 6–7 | | |
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kirillpolevoy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
