---
name: proposal-builder
description: Generate customized sales proposals, business cases, and executive summaries tailored to the prospect's situation. Use this skill whenever a rep needs to create a proposal, write a business case, build an executive summary for a deal, draft a commercial proposal, or says "write a proposal for [company]", "help me build a business case", "I need a proposal document", or "create an executive summary for this deal". Also trigger when building SOW outlines, investment justifications, or mutual action plans. Use when this capability is needed.
metadata:
  author: jbalbu01
---

# Proposal Builder

Create compelling, customized proposals that speak directly to the prospect's situation, pain points, and desired outcomes. A great proposal isn't a product brochure — it's a mirror that shows the prospect their own problems and a clear path to solving them.

## Philosophy

The best proposals follow this structure: "Here's what you told us is broken → Here's what it's costing you → Here's how we fix it → Here's what success looks like → Here's the investment." Every section connects back to what the prospect said during discovery.

---

## How It Works

```
┌─────────────────────────────────────────────────────────────────┐
│                     PROPOSAL BUILDER                              │
├─────────────────────────────────────────────────────────────────┤
│  DOCUMENT TYPES                                                   │
│  1. Full Proposal — Comprehensive document for formal evaluation │
│  2. Executive Summary — 1-2 page overview for senior stakeholders│
│  3. Business Case — ROI-focused justification document            │
│  4. Mutual Action Plan — Shared timeline to close                │
│  5. SOW Outline — Statement of work framework                    │
├─────────────────────────────────────────────────────────────────┤
│  DELIVERY                                                         │
│  • Markdown for quick review and iteration                       │
│  • Word document (.docx) for formal delivery                     │
│  • PDF for polished final version                                │
└─────────────────────────────────────────────────────────────────┘
```

---

## Getting Started

- "Write a proposal for [Company] — here's what I know..."
- "Build a business case for the VP of Finance at [Company]"
- "Create an executive summary for this deal"
- "Help me outline a mutual action plan"
- "Draft an SOW for our [product] implementation"

---

## What I Need From You

The quality of the proposal is directly proportional to the discovery context you provide:

**Must have:**
1. **Company name and context** — Who they are and what they do
2. **The problem** — What's broken, in their words if possible
3. **Your solution** — What you're proposing and why it fits
4. **Key stakeholders** — Who will read this, what they care about
5. **Pricing** — Investment amount and structure

**Makes it much better:**
- Discovery call notes or transcript
- Their stated decision criteria
- Competitor they're evaluating
- Timeline and urgency drivers
- Specific ROI metrics or goals they mentioned
- Their current solution/process and its shortcomings

---

## Output Format: Full Proposal

```markdown
# Proposal: [Your Solution] for [Company Name]

**Prepared for:** [Primary Contact, Title]
**Prepared by:** [Your Name, Title, Your Company]
**Date:** [Date]
**Valid through:** [Expiry date]

---

## Executive Summary

[2-3 paragraphs that summarize: their challenge, the proposed solution, expected outcomes, and the investment. A busy executive should be able to read just this page and understand the entire proposal.]

---

## Understanding Your Situation

[Reflect back what you learned in discovery. Use their language. Show you listened.]

### Current Challenges
[Describe their pain points as they described them]

### Impact on Your Business
[Quantify the cost of inaction — time, money, opportunity cost, risk]

### What Success Looks Like
[Their stated goals and desired outcomes]

---

## Proposed Solution

### Overview
[High-level description of what you're proposing — focused on outcomes, not features]

### How It Works
[Concise explanation of the solution approach]

### Why This Approach
[Connect your solution directly to their stated challenges]

### Key Capabilities
| Their Need | Our Solution | Expected Outcome |
|------------|-------------|------------------|
| [Need 1] | [Capability] | [Result] |
| [Need 2] | [Capability] | [Result] |
| [Need 3] | [Capability] | [Result] |

---

## Implementation Plan

| Phase | Timeline | Activities | Milestone |
|-------|----------|------------|-----------|
| Phase 1: [Name] | [Weeks] | [Key activities] | [Deliverable] |
| Phase 2: [Name] | [Weeks] | [Key activities] | [Deliverable] |
| Phase 3: [Name] | [Weeks] | [Key activities] | [Deliverable] |

---

## Expected Outcomes

### Quantified Benefits
[ROI projections, efficiency gains, cost savings — tied to their metrics]

### Qualitative Benefits
[Team impact, strategic positioning, risk reduction]

### Timeline to Value
[When they can expect to see results]

---

## Investment

| Component | Details | Investment |
|-----------|---------|------------|
| [Line item 1] | [Description] | [$ amount] |
| [Line item 2] | [Description] | [$ amount] |
| **Total** | | **[$ total]** |

**Payment Terms:** [Structure]
**Contract Term:** [Duration]

---

## Why [Your Company]

[Brief — 3-4 points on why you're the right partner. Social proof, relevant experience, differentiators. Not a corporate brochure.]

### Relevant Customer Success
[One or two short examples of similar customers and their results]

---

## Next Steps

1. [Specific action with date]
2. [Second action]
3. [Third action]

---

## Appendix (if needed)

- Detailed technical specifications
- Full ROI model
- Customer references
- Team bios
```

---

## Document Variations

### Executive Summary (1-2 pages)
For senior stakeholders who won't read the full proposal. Hits: problem, solution, outcomes, investment, next steps. Everything in one or two pages.

### Business Case
ROI-focused document for financial stakeholders. Heavy on metrics, cost analysis, payback period, and risk mitigation. Less about the product, more about the investment thesis.

### Mutual Action Plan
Shared document between you and the prospect outlining every step from now to close. Includes owners, dates, and dependencies. Creates accountability on both sides.

### SOW Outline
Implementation-focused document with scope, deliverables, timeline, responsibilities, and acceptance criteria. Used when the prospect is ready to move forward and needs the "how."

---

## Personalization Rules

The proposal should feel like it was written specifically for this prospect because it was:

1. **Use their company name** throughout — not generic "the customer"
2. **Quote their words** — reference specific things they said in discovery
3. **Match their priorities** — lead with what matters most to them
4. **Address their concerns** — proactively respond to known objections
5. **Speak their language** — match industry terminology and tone

---

## Related Skills

- **roi-calculator** — Generate the ROI analysis for the business case section
- **discovery-guide** — Better discovery leads to better proposals
- **deal-qualification** — Ensure the deal is qualified before investing in a proposal
- **battle-cards** — Incorporate competitive positioning if needed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jbalbu01) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
