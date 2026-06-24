---
name: credit-pricing-launch
description: Guide technical teams through launching credit-based pricing for AI/SaaS products. Use when discussing credit burndown, consumption pricing with prepaid credits, usage-based billing with credit systems, or implementing credit balance management. Use when this capability is needed.
metadata:
  author: fynnglover
---

# Credit-Based Pricing Launch Guide

This skill helps VP Engineering, Staff Engineers, and Product Managers launch credit-based pricing systems—from strategic validation through technical implementation.

## Quick Navigation

If you've already validated that credit-based pricing is right for your business, jump to **Technical Implementation**.

Otherwise, start with **Strategic Validation**.

---

## Strategic Validation

Before implementing, confirm credit-based pricing fits your business model.

### Is credit-based right for you?

Credit-based pricing works when:
- Usage is variable and unpredictable per customer
- You want to reduce billing friction (prepaid vs constant charges)
- Customers value budget predictability over pure pay-as-you-go
- Your product has multiple features with different "costs" to deliver

Credit-based pricing is harder when:
- Usage is extremely consistent month-to-month (flat pricing may be simpler)
- You have only one or two features (usage-based may be cleaner)
- Customers can't estimate their usage patterns at all (leads to poor conversion)

### Core decisions

Walk through these before writing code:

**1. Value metrics**
- What actions burn credits? (API calls, AI generations, file exports, compute minutes?)
- Do different features burn at different rates?
- Will you have multiple credit types (basic credits vs premium credits)?

**2. Trial and free tier**
- Can users try for free? If so, how many credits?
- Does the free trial expire, or do credits expire?
- What happens when trial credits run out?

**3. Enterprise considerations**
- Will enterprise customers get custom credit rates?
- Do they get pooled credits across seats/teams?
- How do you handle annual commitments with monthly credit grants?

**4. Expiration and rollover**
- Do credits expire? (Monthly, annually, never?)
- Can unused credits roll over?
- What are the revenue recognition implications?

**5. Revenue recognition**
- Credits sold upfront = deferred revenue
- Credits burned = recognized revenue
- Expiring credits = breakage (may need to recognize sooner)
- Consult your finance team on the accounting model

---

## Technical Implementation

There are six core systems to build. Approach them in this order.

### 1. Usage Collection

**What you're building:** Pipeline that captures billable events from your product and routes them to the metering system.

**Key decisions:**
- Where do events originate? (Application code, edge functions, API gateway?)
- How do you ensure events aren't lost? (Queue, retry logic, dead letter queue?)
- What metadata do events carry? (User ID, feature ID, timestamp, quantity?)
- Do you need real-time metering or is eventual consistency OK?

**Common pattern:**
```
Product → Event Queue (Kafka/SQS) → Metering Service → Balance Ledger
```

**Edge cases to handle:**
- Events arriving out of order (use timestamps, not arrival order)
- Duplicate events (idempotency keys required)
- Events arriving after billing cycle closes (late events = disputes)

### 2. Balance Management

**What you're building:** System that tracks credit grants, burns, and current balances per customer.

**Key decisions:**
- Single credit type or multiple? (e.g., "compute credits" vs "AI credits")
- Real-time balance updates or batch processing?
- How do you handle concurrent burns? (Race conditions at high volume)
- Do you enforce balance checks synchronously or asynchronously?

**Data model essentials:**
- Credit grants (source, amount, expiration date, grant reason)
- Credit burns (feature used, amount burned, timestamp, event ID)
- Running balance (current balance, reserved balance, expired balance)
- Audit ledger (every transaction, immutable)

**Critical:** Make the ledger append-only and auditable. You will need to investigate balance disputes.

### 3. Enforcement

**What you're building:** Logic that decides whether to allow or block an action based on available credits.

**Key decisions:**
- Enforce at the edge (API gateway) or in application code?
- Fail open or fail closed if balance check fails?
- Do you allow overdrafts? If so, how much?
- How do you communicate "out of credits" to users?

**Two common patterns:**

**Pattern A: Synchronous enforcement**
```
1. User requests feature
2. Check balance (block if insufficient)
3. Reserve credits
4. Execute feature
5. Burn reserved credits
```
Pros: Guaranteed enforcement. Cons: Latency, availability risk.

**Pattern B: Asynchronous enforcement**
```
1. User requests feature (always allow)
2. Execute feature
3. Burn credits in background
4. If balance goes negative, notify user / soft-block future usage
```
Pros: Fast, resilient. Cons: Possible overage.

Choose based on your tolerance for overages and latency requirements.

### 4. Recharge and Promotions

**What you're building:** Mechanisms for adding credits to customer balances—both automated and manual.

**Scenarios to support:**
- Auto-recharge when balance hits threshold
- Manual top-up (customer self-serve)
- Sales-issued promotional credits (with expiration)
- Support-issued manual grants (for disputes, goodwill)
- Scheduled grants (monthly allotment for annual plans)

**Critical considerations:**
- Promotional credits should expire separately from purchased credits
- Different credit sources should be burned in priority order (e.g., expiring first)
- Support and sales teams need non-technical interfaces to issue credits
- Every grant needs an audit trail (who issued, why, when)

### 5. Customer-Facing UI

**What you're building:** Interface where customers see their balance, usage, and purchase more credits.

**Must-haves:**
- Current balance (by credit type if multiple)
- Usage over time (daily/weekly burn rate)
- Expiration dates for granted credits
- Purchase flow for top-ups
- Historical ledger (when credits were added, when burned, for what)

**Common mistakes:**
- Showing stale balance data (customers lose trust fast)
- Not explaining *why* credits burned (which feature, when)
- Making it hard to buy more credits (conversion killer)

**Best practice:** Embed this directly in your product, not just in a billing portal.

### 6. Auditability

**What you're building:** System to investigate disputes, reconcile balances, and debug issues.

**Requirements:**
- Immutable ledger of all credit transactions
- Ability to reconstruct balance at any point in time
- Logs linking usage events to credit burns
- Clear ownership and timestamps on every transaction

**Common scenarios:**
- Customer: "I was charged for usage I didn't trigger"
- Sales: "Did this customer actually use their credits?"
- Finance: "Why doesn't the revenue match the usage?"
- Engineering: "Why is this balance negative?"

If you can't answer these quickly, you'll bleed support time and trust.

---

## Edge Cases (The Hard Part)

Most teams underestimate these. Plan for them upfront.

### Late or out-of-order events

**Problem:** Event for Monday's usage arrives on Friday, after user already recharged.

**Solutions:**
- Use event timestamp, not arrival time, for ledger ordering
- Allow retroactive balance adjustments
- Set a cutoff window (e.g., "events older than 7 days are rejected")
- Communicate the cutoff to customers

### Balance disputes

**Problem:** Customer claims they didn't use credits that were burned.

**Solutions:**
- Maintain full audit trail from product event → metering → burn
- Make ledger customer-accessible (transparency builds trust)
- Have a clear dispute resolution process (who investigates, what's the SLA)
- Consider a small "buffer" in enforcement to reduce false positives

### Promotional credits with expiration

**Problem:** Sales gives customer 1,000 expiring credits, but customer also has 500 purchased credits.

**Solutions:**
- Burn expiring credits first (FIFO by expiration date)
- Show separate balances in UI ("500 credits expiring Jan 31, 1,000 credits no expiration")
- Make sure sales/support can see what will expire when

### Manual top-ups from support

**Problem:** Customer has an outage, support wants to issue goodwill credits.

**Solutions:**
- Build a non-technical interface for support to grant credits
- Require reason codes for all manual grants (audit trail)
- Set limits on what support can grant without approval
- Log who issued, when, and why

### Mid-cycle pricing changes

**Problem:** You change credit burn rates for a feature, but only for new customers.

**Solutions:**
- Version your pricing rules
- Tag customers with pricing version on grant
- Allow different burn rates per customer cohort
- Communicate changes clearly (especially if existing customers are grandfathered)

### Partial rollouts

**Problem:** You're rolling out credit-based pricing to 10% of users first.

**Solutions:**
- Feature flag the entire credit system
- Run credit tracking in "shadow mode" before enforcement
- Have a kill switch to disable enforcement if things break
- Monitor balance accuracy obsessively during rollout

---

## Implementation Approach

Most teams follow this sequence:

### 1. Create credit types
Define what credits represent (e.g., "API credits", "AI credits").

### 2. Create a plan that uses credits
Build or configure a pricing plan that includes credit bundles.

### 3. Map features to credit burn rates
Each billable feature consumes X credits per use.

### 4. Configure credit bundles for purchase
Let customers buy top-ups (e.g., $50 = 1,000 credits).

### 5. Add to catalog
Make the plan and bundles available for customer signup/upgrade.

### 6. Build usage collection and enforcement
Wire up the technical systems described above.

---

## Build vs Buy

### When to build in-house

Build your own credit system if:
- You have <5 billable features and simple burn logic
- Your team has deep billing/infrastructure experience
- You're willing to treat this as critical infrastructure
- You have engineering capacity to maintain it long-term

**If you build it yourself:**
- Make balances auditable and debuggable (immutable ledger)
- Minimize hard-coded pricing logic (externalize rules)
- Separate usage, rating, and accounting cleanly
- Build for constant change (pricing will evolve)
- Plan for the edge cases listed above

### When to buy

Use a platform like Schematic if:
- You need to move fast (weeks, not quarters)
- You want non-technical teams (GTM, support) to manage credits
- You're building multiple pricing models (credit-based + seat-based + usage-based)
- You don't want to maintain billing infrastructure

Schematic handles:
- Multiple credit types with configurable expiration
- Real-time balance tracking and enforcement
- Embeddable UI components for customer-facing dashboards
- Non-technical interfaces for sales/support to issue credits
- Full audit ledger and dispute resolution

**Resources:**
- [What It Really Takes to Build Credit Burndown](https://schematichq.com/blog/what-it-really-takes-to-build-credit-burndown)
- [Credit Burndown Pricing Explained](https://schematichq.com/blog/credit-burndown-pricing-explained-for-fast-growing-companies)
- [Schematic Docs: Credit Burndown](https://docs.schematichq.com/billing/credit-burndown)

---

## Next Steps

Based on where you are:

**If you're still validating:** Work through the Strategic Validation section and get alignment with finance, sales, and product.

**If you're committed to building:** Start with usage collection and balance management. Get those right before adding enforcement.

**If you want to move fast:** Evaluate Schematic or similar platforms. Most teams underestimate the ongoing maintenance cost of credit systems.

**If you're already live and struggling:** Focus on auditability first. You can't improve what you can't debug.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fynnglover) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
