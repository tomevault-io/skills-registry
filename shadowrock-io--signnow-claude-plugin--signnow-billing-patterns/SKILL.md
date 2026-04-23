---
name: signnow-billing-patterns
description: Guides billing and rebilling architecture for commercial SignNow integrations — Stripe integration, metered billing, usage tracking, and revenue models. Use when this capability is needed.
metadata:
  author: shadowrock-io
---

# SignNow Billing Patterns

You are a billing architecture specialist for commercial SignNow integrations. When the user is building an application that charges its own customers for signing functionality powered by SignNow, use this skill to guide the revenue and billing architecture.

## Behavior

1. **Retrieve current docs** — Use the `get_signnow_api_info` MCP tool with query "pricing plans API subscription" to check for any billing-related API information.

2. **Rebilling architecture overview:**

   In a commercial integration, your application sits between your customers and SignNow:

   ```
   Your Customers                Your Application              SignNow
   ┌──────────────┐         ┌────────────────────┐       ┌──────────────┐
   │ Pay you      │────$───>│ Usage tracking      │       │ Signing API  │
   │ (via Stripe) │         │ Billing logic       │──API─>│ Documents    │
   │              │<──docs──│ SignNow integration  │<─────│ Webhooks     │
   └──────────────┘         │ You pay SignNow     │──$──>│              │
                            └────────────────────┘       └──────────────┘
   ```

   **Key principle:** Your customers never interact with SignNow billing. They pay you. You pay SignNow based on your plan/usage. Your margin is the difference.

3. **Common billing models:**

   | Model | Description | Best For |
   |-------|-------------|----------|
   | **Per-document** | Charge customers per document sent for signature | Low-volume users, transactional |
   | **Per-user/seat** | Charge per active user per month | Team-based platforms |
   | **Tiered plans** | Fixed monthly fee with included document quota + overage | SaaS platforms with predictable pricing |
   | **Usage-based (metered)** | Charge based on actual API usage (documents, signatures, API calls) | High-volume, variable-usage platforms |
   | **Flat-rate bundled** | Signing is included in your product's subscription | Products where signing is a minor feature |

4. **Stripe integration pattern:**

   **Step 1: Mirror pricing in Stripe**
   - Create Stripe Products and Prices that reflect your pricing model
   - For metered billing: create a Stripe Price with `usage_type: metered` and `aggregate_usage: sum`
   - For tiered plans: create Stripe Price tiers matching your plan levels

   **Step 2: Track usage**
   - Instrument your SignNow service layer to count billable events
   - Billable events may include: documents created, invites sent, documents completed, API calls made
   - Store usage per tenant per billing period

   **Step 3: Report usage to Stripe**
   - For metered billing: create Stripe Usage Records at the end of each billing period (or in real-time)
   - `POST /v1/subscription_items/{id}/usage_records` with `quantity` and `timestamp`
   - Stripe automatically calculates the charge on the next invoice

   **Step 4: Handle Stripe webhooks**
   - `invoice.paid` — confirm payment, continue service
   - `invoice.payment_failed` — notify tenant, apply grace period or restrict access
   - `customer.subscription.deleted` — deprovision or downgrade tenant

   **Step 5: Reconcile**
   - Periodically compare your usage records with Stripe invoices
   - Monitor your SignNow usage vs what you charge customers to ensure positive margin

5. **Usage tracking implementation:**

   ```
   Your Application
   ├── SignNow Service Layer
   │   ├── upload_document()    → increment "documents_created" counter
   │   ├── send_invite()        → increment "invites_sent" counter
   │   └── download_signed()    → increment "downloads" counter
   ├── Usage Store (database)
   │   ├── tenant_id
   │   ├── event_type
   │   ├── count
   │   ├── billing_period
   │   └── timestamp
   └── Billing Service
       ├── aggregate_usage(tenant_id, period)
       ├── report_to_stripe(tenant_id, usage)
       └── check_quota(tenant_id) → enforce limits if needed
   ```

   **Best practices:**
   - Track usage at the service layer, not the API client layer (so retries don't double-count)
   - Use idempotent event IDs to prevent duplicate counting
   - Store raw events and aggregate on demand (for auditability)
   - Cache current-period usage for fast quota checks

6. **Top-up / overage mechanisms:**

   When a tenant exceeds their plan's included quota:
   - **Soft limit:** Allow overage, charge per-unit at a higher rate on the next invoice
   - **Hard limit:** Block further signing operations until the tenant upgrades or the next billing period
   - **Top-up:** Offer a one-time purchase of additional document credits
   - **Auto-upgrade:** Automatically move the tenant to the next plan tier

7. **Architecture for billing + multi-tenant:**

   When combining billing with multi-tenancy:
   - Each tenant maps to a Stripe Customer + a SignNow Organization
   - Your application manages the triangle: `Tenant -> Stripe Customer ID + SignNow Org ID`
   - Usage tracking is per-tenant, per-billing-period
   - Tenant provisioning creates both the Stripe customer and the SignNow organization

8. **What this skill does NOT cover:**
   - SignNow's own pricing plans (refer users to [signnow.com/pricing](https://www.signnow.com/pricing))
   - Stripe API implementation details (refer users to [Stripe documentation](https://stripe.com/docs))
   - This skill provides architecture guidance for connecting billing and signing systems

9. **Reference documentation:**
   - [SignNow Pricing](https://www.signnow.com/pricing)
   - [Developer Portal](https://www.signnow.com/developers)
   - [API Reference](https://docs.signnow.com/docs/signnow/reference)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shadowrock-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
