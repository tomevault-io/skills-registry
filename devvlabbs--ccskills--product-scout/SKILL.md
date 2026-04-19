---
name: product-scout
description: Systematically find and analyze successful products in any industry to identify opportunities for cloning or rebranding. Use when researching competitors, evaluating market opportunities, performing due diligence on products to potentially clone, or analyzing business models for adaptation to new markets. Use when this capability is needed.
metadata:
  author: devvlabbs
---

# Product Scout

Systematic discovery and analysis of successful products across any industry, producing comprehensive reports that inform cloning and rebranding decisions.

## When to Use This Skill

- Researching successful products to clone or rebrand
- Analyzing competitors in a specific market
- Evaluating market opportunities before building
- Performing due diligence on business models
- Identifying gaps in existing product offerings
- Understanding pricing strategies in a niche
- Discovering successful products in adjacent markets

## Scouting Modes

### Quick Scout (Default)
Fast discovery producing a shortlist of 3-5 candidates with brief descriptions. Use when exploring options before committing to deep analysis.

**Output:** Shortlist table with name, description, why interesting, initial score (1-5)

### Deep Analysis
Comprehensive analysis of a specific product. Use after Quick Scout when user selects a candidate.

**Output:** Full analysis report with 7-dimension scoring

**Workflow:** Quick Scout → User picks candidate → Deep Analysis → brand-namer skill for naming

---

## Scouting Process

### Step 1: Define Search Parameters

Gather from the user:
- **Industry/Niche**: What market or category to scout
- **Scout Mode**: Quick Scout (default) or Deep Analysis of specific product
- **Target Region**: Where will the clone operate (affects pricing, localization)
- **Geography of Products**: Global, regional, or specific country focus
- **Stage**: Startups, scale-ups, or established companies
- **Revenue Model**: SaaS, marketplace, e-commerce, etc.
- **Funding Status**: Bootstrapped, VC-backed, or any

### Step 2: Systematic Discovery

Use multiple scouting channels in parallel. Reference `references/scouting-sources.md` for the comprehensive source list organized by category:

**General Discovery**: CB Insights, Crunchbase, Product Hunt, Y Combinator directory, App Store/Play Store rankings

**Industry News**: TechCrunch, Finance Magnates (fintech), industry-specific publications

**Reviews & Rankings**: G2, Capterra, Trustpilot, industry comparison sites

**Social Signals**: Reddit, Twitter/X, Hacker News, industry forums

**Funding Data**: Crunchbase funding rounds, PitchBook coverage

### Step 3: Initial Product Shortlist

Create a shortlist of 5-10 promising products with:
- Product name and URL
- One-line description
- Why it appears on radar (funding, reviews, traction)
- Initial viability score (1-5)

### Step 4: Deep Analysis

For each shortlisted product, produce a complete analysis report using the template below.

## Analysis Report Template

Reference `references/analysis-framework.md` for detailed methodology.

```markdown
# Product Analysis: [Product Name]

## Executive Summary
[2-3 sentence overview of the product and clone opportunity]

---

## 1. Business Model Breakdown

### Revenue Model
- [ ] SaaS (subscription)
- [ ] Marketplace (transaction fees)
- [ ] Freemium
- [ ] One-time purchase
- [ ] Usage-based
- [ ] Advertising

### Monetization Details
- Primary revenue stream: [description]
- Secondary revenue streams: [if any]
- Estimated revenue range: [if available]

---

## 2. Core Features & USPs

### Must-Have Features (MVP)
1. [Feature 1] - [brief description]
2. [Feature 2] - [brief description]
3. [Feature 3] - [brief description]

### Differentiating Features
1. [What makes this product stand out]
2. [Unique capabilities]

### Feature Gaps Identified
- [Missing features mentioned in reviews]
- [Feature requests from users]

### Market Gaps Identified
- [Underserved regions/languages]
- [Underserved customer segments]
- [Adjacent use cases not addressed]

**Example:** PropGPT serves US sports bettors in English. Market gap = Arabic-speaking football bettors (different sport, different language, different region).

---

## 3. Pricing Structure

| Tier | Price | Key Features |
|------|-------|--------------|
| Free | $0 | [features] |
| Basic | $X/mo | [features] |
| Pro | $Y/mo | [features] |
| Enterprise | Custom | [features] |

### Regional Pricing Considerations
If targeting a different region than the original product:

| Region | PPP Adjustment | Notes |
|--------|----------------|-------|
| US/EU | 1.0x (baseline) | Full pricing |
| MENA | 0.5-0.7x | Lower purchasing power |
| SE Asia | 0.3-0.5x | Price sensitive |
| LATAM | 0.4-0.6x | Varies by country |

**Example:** US product at $29/mo → MENA clone at $15-20/mo may capture same market segment.

---

## 4. Target Market & Customer Segments

### Primary Customer Segment
- **Who**: [demographic/firmographic]
- **Pain point**: [what problem they have]
- **Willingness to pay**: [estimated]

### Market Size Indicators
- TAM estimate: [if available]
- Competition density: [high/medium/low]

---

## 5. Competitive Landscape

| Competitor | Positioning | Key Difference |
|------------|-------------|----------------|
| [Name] | [position] | [difference] |

---

## 6. Technology Stack (if identifiable)

- Framework: [React/Vue/Angular/etc.]
- Infrastructure: [AWS/GCP/Azure/etc.]
- Key integrations: [list visible integrations]

Detection methods: BuiltWith, Wappalyzer, job postings, GitHub presence

---

## 7. Strengths & Weaknesses

### Strengths
1. [Strength with evidence]
2. [Strength with evidence]

### Weaknesses
1. [Weakness from reviews]
2. [Weakness from reviews]

### Opportunities for a Clone
1. [Opportunity 1]
2. [Opportunity 2]

---

## 8. Clone Opportunity Assessment

Reference `references/clone-assessment-criteria.md` for scoring methodology.

### Viability Score: [X/10]

| Criterion | Score | Notes |
|-----------|-------|-------|
| Market demand evidence | /10 | |
| Technical complexity | /10 | (lower = easier to clone) |
| Regulatory barriers | /10 | (lower = easier) |
| Network effects moat | /10 | (lower = easier) |
| Geographic opportunity | /10 | |
| Pricing room | /10 | |
| Feature gap opportunity | /10 | |

### Clone Recommendation
- [ ] **Strong Clone Candidate**: Clear path to market
- [ ] **Moderate Opportunity**: Some barriers but addressable
- [ ] **Weak Opportunity**: Significant challenges
- [ ] **Not Recommended**: Strong moats or saturated

### Suggested Differentiation
1. [How to differentiate: geography, pricing, features]
2. [Additional differentiation]
```

## Example Usage

**Basic Request:**
```
Scout successful fintech products in the "buy now pay later" space.
I'm looking for products to potentially clone for the Middle East market.
```

**Specific Company Analysis:**
```
Analyze Robinhood's business model and assess clone viability
for the European market.
```

**Industry Sweep:**
```
Find the top 5 most promising B2B SaaS products in the HR tech space
that have raised Series A funding in the last 12 months.
```

## Tips for Best Results

- **Be specific about geography**: Clone viability varies greatly by region
- **Specify funding stage**: Bootstrapped vs VC-backed have different models
- **Mention your constraints**: Budget, timeline, team size affect recommendations
- **Share differentiation ideas**: Helps assess if the angle is viable
- **Request multiple products**: Comparison reveals market patterns
- **Start with Quick Scout**: Don't deep-dive until you've seen the landscape

## Next Steps After Scouting

Once you've identified a clone candidate:

1. **Use brand-namer skill** to generate names for your clone
2. **Verify domain/brand availability** before committing
3. **Write a PRD** based on the analysis report
4. **Validate with target users** in your region before building

## Resources

This skill includes reference documentation:
- `references/scouting-sources.md` - Complete list of 50+ discovery channels by category
- `references/analysis-framework.md` - Deep-dive analysis methodology
- `references/clone-assessment-criteria.md` - 7-dimension scoring and evaluation criteria

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/devvlabbs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
