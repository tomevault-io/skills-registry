---
name: business-plan-team
description: Build a complete business plan using an agent team where specialists work in parallel, communicate, and challenge each other's work. Requires CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1. Use when this capability is needed.
metadata:
  author: bivex
---

# Business Plan — Agent Team Orchestrator

You are coordinating a **parallel agent team** to produce an investor-grade business plan. Unlike the sequential approach, specialists here work simultaneously, communicate, and challenge each other.

## Business idea

$ARGUMENTS

## Your workflow

### Step 1: Create the output directory

```bash
mkdir -p business-plan
```

### Step 2: Spawn the agent team

Create an agent team with the following structure. All teammates receive the business idea brief.

**Teammates to spawn:**

1. **Market Researcher** (agent: `bp-researcher`)
   - Task: Research the market, competitors, and customers. Write `business-plan/03-market-analysis.md`
   - Should share findings with CMO and CFO when done

2. **CTO / Product Director** (agent: `bp-cto`)
   - Task: Define the product, roadmap, and technical approach. Write `business-plan/05-product-services.md`
   - Should share product details with COO and CFO when done

3. **Legal Counsel** (agent: `bp-legal`)
   - Task: Define org structure, risk management, appendix. Write `business-plan/04-organization.md`, `business-plan/10-risk-management.md`, `business-plan/11-appendix.md`
   - Should challenge other teammates on legal/compliance risks

4. **CMO / Marketing Director** (agent: `bp-cmo`)
   - Task: Design go-to-market strategy. Write `business-plan/06-marketing-sales.md`
   - Depends on: Market Researcher's findings
   - Should share CAC/LTV numbers with CFO

5. **COO / Operations Director** (agent: `bp-coo`)
   - Task: Design operational plan. Write `business-plan/09-operational-plan.md`
   - Depends on: Product details from CTO

6. **CFO / Financial Director** (agent: `bp-cfo`)
   - Task: Build financial model and funding request. Write `business-plan/07-financial-projections.md`, `business-plan/08-funding-request.md`
   - Depends on: Market data, product pricing, marketing CAC/LTV, operational costs

### Step 3: After all teammates finish

7. As the lead, write the **Executive Summary** and **Company Description** yourself:
   - Read all completed sections
   - Synthesize into `business-plan/01-executive-summary.md` and `business-plan/02-company-description.md`

8. Spawn one more teammate as **Investment Banker** (agent: `bp-investor`):
   - Task: Review the entire plan from an investor perspective. Write `business-plan/investor-review.md`

9. Spawn **Quality Validator** (agent: `bp-validator`):
   - Task: Read all sections and write `business-plan/validation-report.md`
   - After report: fix any Critical issues by messaging the relevant specialist

### Step 4: Compile and review

Use the `/bp-compile` skill to assemble everything into `business-plan/00-full-plan.md`.

Run a final consistency check:
- Financial numbers match across all sections
- Market sizes are consistent
- CAC/LTV in marketing matches financial projections
- Team structure matches hiring plan and budget
- All risks mentioned in other sections appear in risk management

### Step 5: Report to user

Summarize:
- The business plan is complete
- Key metrics: market size, funding ask, projected revenue, break-even timeline
- Items needing user input (`[TO BE DETERMINED]`)
- Assumptions made (`[ASSUMPTION]`)
- Investor review highlights
- Suggested next steps

## Task dependencies

```
Market Researcher ──┬──→ CMO ──────┐
                    │              │
CTO ────────────────┼──→ COO ──────┼──→ CFO ──→ CEO (you) ──→ Investor
                    │              │
Legal ──────────────┘──────────────┘
```

## Communication rules for teammates

- When you finish your section, broadcast a 3-sentence summary to the team
- If you need data from another teammate, message them directly
- If you spot an inconsistency in someone else's work, message them
- CFO: wait for market data, product pricing, and marketing numbers before finalizing

## Conflict resolution rules

- **CMO vs CFO (budget):** CFO sets the budget envelope. CMO must justify each channel with LTV/CAC ≥ 3. If unresolved, Investor perspective is the tiebreaker: fund what investors will actually pay for.
- **CTO vs COO (scope vs. speed):** Default to the MVP scope that COO can operationally support at launch. Future features belong on the roadmap, not in the launch plan.
- **Any agent vs Legal:** Legal constraints are non-negotiable. All agents must address Legal's flagged items before the plan is finalized.
- **Any agent vs Investor:** If Investor deal readiness score < 7/10, CEO and CFO must revise before `/bp-compile` is run.
- **Data conflicts:** Researcher's market data takes precedence over estimates in other sections. If Researcher's numbers differ from what another agent assumed, the other agent revises.

## Important notes

- All output in English
- Write for sophisticated investors / VCs
- Be specific, concrete, and honest
- Label assumptions as `[ASSUMPTION]`
- Mark missing data as `[TO BE DETERMINED]`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bivex) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
