---
name: expansion-playbook
description: Create upsell, cross-sell, and renewal playbooks for existing customers — identify expansion signals, build business cases, and run expansion conversations. Use this skill whenever a CS or sales team wants to grow existing accounts, identify upsell opportunities, prepare for renewal conversations, build expansion strategies, or says "how do I upsell [customer]", "renewal strategy", "cross-sell opportunity", "expand this account", or "net revenue retention". Also trigger when someone mentions land-and-expand, account expansion, customer growth, or NRR. Use when this capability is needed.
metadata:
  author: jbalbu01
---

# Expansion Playbook

Growing existing customers is cheaper and faster than acquiring new ones. This skill helps CS and sales teams identify expansion signals, build compelling business cases, and run expansion conversations that feel like partnership — not a sales pitch.

## Why Expansion Matters

- **3-5x cheaper** to expand an existing customer than acquire a new one
- **NRR (Net Revenue Retention)** is the #1 metric investors and boards care about
- Happy customers who expand become your strongest references
- Expansion validates your product-market fit in the deepest way

---

## How It Works

```
┌─────────────────────────────────────────────────────────────────┐
│                  EXPANSION PLAYBOOK                               │
├─────────────────────────────────────────────────────────────────┤
│  PLAYS                                                            │
│  1. Upsell — More of the same (more seats, higher tier)          │
│  2. Cross-sell — New products or modules                         │
│  3. Renewal — Protect and grow at renewal                        │
│  4. Land-and-Expand — Strategy from initial deal to full deploy  │
│                                                                   │
│  SIGNALS                                                          │
│  • Usage patterns (high adoption → ready for more)               │
│  • Team growth (hiring → need more seats)                        │
│  • New use cases (expanding beyond original intent)              │
│  • Champion promotion (your advocate now has more budget)        │
│  • Competitor displacement (using something else for adjacent)   │
│  • Contract milestones (renewal approaching, initial ROI proven) │
├─────────────────────────────────────────────────────────────────┤
│  SUPERCHARGED (when you connect your tools)                      │
│  + ~~CRM: Customer deal history, ARR, and contract dates         │
│  + ~~CRM: Contact roles and champion mapping                     │
│  + ~~CRM: Usage data and health scores (if tracked)              │
│  + ~~data enrichment (ZoomInfo): Company growth and hiring       │
│  + ~~data enrichment (ZoomInfo): Tech stack changes              │
│  + ~~data enrichment (Clay): Expansion signals and triggers      │
│  + ~~data enrichment (LinkedIn): Champion job changes            │
│  + ~~data enrichment (LinkedIn): Company news and updates        │
│  + ~~chat: Internal customer discussions and feedback             │
│  + ~~calendar/email: Recent customer correspondence              │
└─────────────────────────────────────────────────────────────────┘
```

---

## Execution Flow

### Step 0: Automatic Data Pull (Before Asking the User Anything)

**CRITICAL:** Before asking about the customer, pull everything from connected tools. Expansion intelligence is most powerful when grounded in real account data, growth signals, and relationship history.

#### CRM Data Pull

Check if you have access to CRM tools (look for tools containing `search_crm_objects`, `get_crm_objects`, or similar).

If CRM tools ARE available:

1. **Find the customer's deals.** Search `deals` for the company name — pull all deals (won, open, lost).
   - Properties: `dealname`, `amount`, `dealstage`, `closedate`, `createdate`, `pipeline`, `hubspot_owner_id`, `dealtype`, `description`
   - Calculate: current ARR, total contract value, deal history timeline
   - Identify: what products/modules they bought, what they didn't
2. **Pull contacts.** Get all associated contacts for the customer.
   - Properties: `firstname`, `lastname`, `jobtitle`, `email`, `phone`, `lifecyclestage`
   - Map: champion (your advocate), economic buyer, power users, new stakeholders added since initial deal
3. **Pull company.** Get company profile.
   - Properties: `name`, `domain`, `industry`, `numberofemployees`, `annualrevenue`, `description`, `founded_year`
   - Compare current employee count to when they signed — growth = expansion signal
4. **Check renewal date.** Look for contract end date or renewal fields.
   - If renewal within 6 months → trigger renewal strategy section
5. **Pull deal notes and activity.** Check for support tickets, NPS scores, or health metrics if tracked.

#### Sales Intelligence Data Pull

**ZoomInfo** (if available):
1. **Get current company data.** Use `zoominfo_search_company` with the customer company name/domain.
   - Employee count change since they signed → hiring = seat expansion signal
   - Revenue growth → validates they can afford expansion
   - New departments or initiatives → cross-sell opportunities
2. **Get org chart.** Use `zoominfo_get_org_chart` for the customer.
   - New leaders or VPs hired → potential new buying centers
   - Champion's current reporting structure → has their scope grown?
3. **Get tech stack.** Use `zoominfo_get_tech_stack` for the customer.
   - New tools adopted → integration opportunities or adjacent needs
   - Competitor tools still in use → displacement opportunity

**Clay** (if available):
1. **Enrich the company.** Use `clay_enrich_company` with customer domain.
   - Growth signals, funding events, product launches → expansion triggers
2. **Trigger enrichment.** Use `clay_trigger_enrichment` for expansion-specific signals.

**LinkedIn** (if available):
1. **Check champion status.** Use `linkedin_get_profile` for your champion contact.
   - Promoted? → More budget and influence — perfect upsell timing
   - Left the company? → Risk alert — identify new champion immediately
2. **Search company updates.** Use `linkedin_search_companies` for customer company.
   - Hiring trends, new offices, product launches → expansion signals
   - Strategic initiatives they announced → align expansion to their priorities

#### Gong Data Pull

Check if you have access to Gong tools (look for tools prefixed with `gong_`).

If Gong tools ARE available:
1. **Find recent calls with this customer.** Use `gong_search_calls` with the company name.
2. **Pull transcripts from QBRs or check-ins.** Use `gong_get_transcript`.
   - Extract: new pain points mentioned, feature requests, adjacent use cases discussed
   - Extract: satisfaction signals or risk signals
   - Extract: mentions of other departments wanting access

#### Chat Data Pull

If chat tools are available (`slack_search_public`, `slack_search_public_and_private`):
1. Search for the customer name in CS and sales channels
2. Surface: customer health discussions, feature requests, support escalations, expansion discussions
3. Look for champion feedback or internal advocacy signals

#### Calendar/Email Data Pull

If calendar/email tools are available:
1. Search for recent meetings with the customer — frequency and recency of engagement
2. Check for any expansion discussions already in progress

#### Present What You Found

> "I pulled the full account picture for **[Customer]**: **$[X] current ARR** across **[N] deals** since [date]. Per CRM, they have **[N] contacts** with **[N] users** on [products]. Per ZoomInfo, they've grown from **[N] to [N] employees** since signing (+[X]%). [If LinkedIn:] Your champion **[Name]** was recently **promoted to [new title]**. [If Gong:] On the last QBR, they mentioned [new need/pain]. Expansion signals detected — building your playbook now..."

### Step 1: Gather Remaining Context

After the auto-pull, ask ONLY for what the tools couldn't provide:
- **Customer health** — How happy are they? Any open issues or risks?
- **Relationship dynamics** — Who's your best contact? Any internal politics to navigate?
- **Expansion goal** — Upsell, cross-sell, renewal, or full land-and-expand strategy?
- **Products available** — What else could they buy? (if not in memory/product.md)

### Step 2: Generate the Expansion Plan

Build using ALL evidence. Cite sources: "Per CRM:", "Per ZoomInfo:", "Per LinkedIn:", "Per Gong:", "Per Slack:", "User reported:"

### Step 3: Store Insights

- Update `memory/deal-patterns.md` with expansion patterns (what triggers work)
- Update `memory/team.md` with CSM/AE expansion skills and patterns
- Log the expansion plan in `memory/changelog.md`

---

## Getting Started

- "Build an expansion strategy for [Customer]"
- "How should I approach the renewal conversation with [Customer]?"
- "What upsell opportunities exist in our customer base?"
- "[Customer] just hired 50 people — should I reach out?"
- "Create a land-and-expand playbook for our enterprise motion"

---

## Output Format: Account Expansion Plan

```markdown
# Expansion Plan: [Customer Name]

**CSM:** [Name] | **AE:** [Name]
**Current ARR:** $[amount]
**Contract Renewal:** [Date]
**Expansion Potential:** $[amount] | [X]% confidence

---

## Current State
| Metric | Value |
|--------|-------|
| Users / Seats | [Current] of [Licensed] |
| Adoption Rate | [X]% |
| Health Score | [X]/100 |
| NPS / CSAT | [Score] |
| Modules Used | [List] |
| Modules Available | [Not yet purchased] |

## Expansion Signals Detected

| Signal | Evidence | Opportunity |
|--------|----------|-------------|
| 🟢 [Signal] | [Specific evidence] | [What to propose] |
| 🟢 [Signal] | [Evidence] | [Opportunity] |
| 🟡 [Signal] | [Weaker evidence] | [Worth exploring] |

## Recommended Plays

### Play 1: [Upsell/Cross-sell/Tier Upgrade]
**What:** [Specific expansion proposal]
**Why Now:** [Trigger or signal that makes this timely]
**Value to Customer:** [In their terms — not yours]
**Estimated ARR Impact:** $[amount]
**Approach:** [How to position this conversation]
**Talk Track:**
> "[Opening that connects to their business goals, not your quota]"

### Play 2: [Second opportunity]
[Same structure]

---

## Renewal Strategy (if renewal within 6 months)

**Renewal Date:** [Date]
**Risk Level:** 🟢🟡🔴
**Expansion at Renewal:** [Likely / Possible / Unlikely]

### Pre-Renewal Timeline
| Weeks Before | Action | Owner |
|-------------|--------|-------|
| 12 weeks | Executive business review | CSM |
| 8 weeks | Renewal + expansion proposal | AE |
| 4 weeks | Negotiate and finalize | AE |
| 2 weeks | Contract execution | Legal |

### Renewal Conversation Guide
1. Start with value delivered (their ROI, not your features)
2. Review success against original criteria
3. Introduce expansion as a natural next step
4. Handle any concerns proactively
```

---

## Related Skills

- **handoff-builder** → Initial handoff identifies expansion opportunities
- **roi-calculator** → Build expansion business case with ROI data
- **proposal-builder** → Create formal expansion proposals
- **deal-qualification** → Qualify expansion opportunities rigorously

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jbalbu01) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
