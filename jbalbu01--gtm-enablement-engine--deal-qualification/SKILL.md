---
name: deal-qualification
description: Qualify sales deals using MEDDIC, BANT, SPICED, or CHAMP frameworks with structured scoring and gap analysis. Use this skill whenever a rep needs to qualify a deal, assess deal health, decide whether to invest more time in an opportunity, says "should I pursue this deal", "qualify this opportunity", "MEDDIC score", "BANT check", or asks about deal qualification criteria. Also trigger when a manager reviews pipeline quality or when someone mentions deal scoring, opportunity assessment, or go/no-go decisions. Use when this capability is needed.
metadata:
  author: jbalbu01
---

# Deal Qualification

Help reps and managers objectively assess whether a deal is worth pursuing, identify gaps in qualification, and decide where to invest their time. The worst thing in sales is spending months on a deal that was never going to close — qualification prevents that.

## Frameworks

### MEDDIC (Recommended for Enterprise B2B SaaS)
The gold standard for complex, multi-stakeholder deals:

- **M — Metrics:** What quantifiable outcomes does the prospect expect?
- **E — Economic Buyer:** Who has the budget authority and final say?
- **D — Decision Criteria:** What factors will they use to choose a vendor?
- **D — Decision Process:** What are the steps, stakeholders, and timeline?
- **I — Identify Pain:** What specific problem are they solving?
- **C — Champion:** Who inside the account is actively selling for you?

### BANT (Good for Simpler or Faster Sales Cycles)
- **B — Budget:** Do they have money allocated?
- **A — Authority:** Are you talking to the decision-maker?
- **N — Need:** Do they have a real problem you solve?
- **T — Timeline:** Is there urgency to act?

### SPICED (Good for Customer-Centric Selling)
- **S — Situation:** What's their current state?
- **P — Pain:** What's broken or missing?
- **I — Impact:** What happens if they don't fix it?
- **C — Critical Event:** Is there a forcing function or deadline?
- **E — Economic Decision:** Who decides and how?
- **D — Decision Criteria:** How will they evaluate options?

---

## How It Works

```
┌─────────────────────────────────────────────────────────────────┐
│                   DEAL QUALIFICATION                              │
├─────────────────────────────────────────────────────────────────┤
│  MODES                                                            │
│  1. Score a Deal — Rate a specific opportunity on all criteria    │
│  2. Gap Analysis — Identify what's missing and how to fill it    │
│  3. Go/No-Go — Make a structured decision on whether to pursue   │
│  4. Pipeline Audit — Score multiple deals for prioritization      │
├─────────────────────────────────────────────────────────────────┤
│  OUTPUT                                                           │
│  • Qualification scorecard with red/yellow/green per criterion   │
│  • Gap analysis with specific next actions                       │
│  • Risk assessment and overall confidence level                  │
│  • Comparison against ideal deal profile                         │
└─────────────────────────────────────────────────────────────────┘
```

---

## Getting Started

- "Score this deal using MEDDIC"
- "Is this deal worth pursuing? Here's what I know..."
- "What gaps do I have in qualifying [Company]?"
- "Review my pipeline — which deals are real?"
- "I have a go/no-go meeting — help me prepare"

---

## What I Need From You

Tell me everything you know about the deal. I'll figure out what's missing.

1. **Company and deal size** — Who and how big?
2. **What you're selling** — Product, use case, proposed solution
3. **Who you're talking to** — Names, titles, roles in the decision
4. **What you've learned** — Pain points, requirements, timeline
5. **Where you are in the process** — Stage, next steps, blockers
6. **Framework preference** — MEDDIC, BANT, SPICED, or "you decide"

---

## Output Format: Deal Scorecard

```markdown
# Deal Qualification: [Company Name]

**Date:** [Today]
**Framework:** [MEDDIC]
**Deal Size:** [$ amount]
**Stage:** [Current stage]
**Rep:** [Name]

---

## Qualification Score: [X / 30]

| Criterion | Score (0-5) | Status | Evidence |
|-----------|------------|--------|----------|
| **Metrics** | [0-5] | 🟢🟡🔴 | [What we know] |
| **Economic Buyer** | [0-5] | 🟢🟡🔴 | [What we know] |
| **Decision Criteria** | [0-5] | 🟢🟡🔴 | [What we know] |
| **Decision Process** | [0-5] | 🟢🟡🔴 | [What we know] |
| **Identify Pain** | [0-5] | 🟢🟡🔴 | [What we know] |
| **Champion** | [0-5] | 🟢🟡🔴 | [What we know] |

**Score Guide:** 🟢 25-30 (Strong) | 🟡 15-24 (Needs Work) | 🔴 0-14 (At Risk)

---

## Confidence Level: [High / Medium / Low]

[One paragraph assessment of overall deal health and likelihood to close]

---

## Gap Analysis

### Critical Gaps (Must Fill)
1. **[Gap]** — [Why it matters] — **Action:** [Specific step to fill this gap]
2. **[Gap]** — [Why it matters] — **Action:** [Step]

### Important Gaps (Should Fill)
1. **[Gap]** — **Action:** [Step]

### Nice to Have
1. **[Gap]** — **Action:** [Step]

---

## Risk Assessment

| Risk | Severity | Mitigation |
|------|----------|------------|
| [Risk 1] | High/Med/Low | [What to do] |
| [Risk 2] | ... | ... |

---

## Recommended Next Steps

1. [Most important action with specific guidance]
2. [Second priority]
3. [Third priority]

---

## Go / No-Go Recommendation

**Recommendation:** [PURSUE / PURSUE WITH CAUTION / DEPRIORITIZE / WALK AWAY]

**Rationale:** [Why — based on the evidence]

**Conditions to Continue:** [What must be true to keep investing in this deal]
```

---

## Scoring Guide

Each criterion scores 0-5:

- **5 — Fully Validated:** Clear evidence, confirmed by the prospect
- **4 — Strong Indication:** Good evidence but not fully confirmed
- **3 — Partial:** Some information but significant gaps remain
- **2 — Assumed:** Based on inference, not direct confirmation
- **1 — Unknown:** We know we need this but don't have it
- **0 — Missing/Negative:** No information or actively concerning signals

---

## Pipeline Audit Mode

When reviewing multiple deals, I'll produce a ranked list:

```markdown
# Pipeline Qualification Audit

| Rank | Deal | Size | Score | Confidence | Key Risk | Action |
|------|------|------|-------|------------|----------|--------|
| 1 | [Deal A] | $X | 26/30 | High | [Risk] | [Action] |
| 2 | [Deal B] | $X | 21/30 | Medium | [Risk] | [Action] |
| ... | ... | ... | ... | ... | ... | ... |

## Summary
- **Strong deals:** [Count] worth $[total]
- **At-risk deals:** [Count] worth $[total] — need attention this week
- **Recommended deprioritize:** [Count] worth $[total]
```

---

## Related Skills

- **discovery-guide** — Fill qualification gaps with better discovery
- **proposal-builder** — Build proposals for well-qualified deals
- **win-loss-analysis** — Learn from past deal outcomes to improve qualification
- **roi-calculator** — Quantify the Metrics criterion

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jbalbu01) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
