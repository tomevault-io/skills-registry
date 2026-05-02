---
name: solvapay
description: Payment management tools for SolvaPay — manage customers, products (with embedded plans), purchases, payments, usage, and transactions. Use when this capability is needed.
metadata:
  author: solvapay
---

# SolvaPay Payment Management

You have access to SolvaPay payment tools (prefixed `solvapay_`). Use them to manage the provider's payment platform.

## Tool Domains

### Customers
- `solvapay_create_customer` — Create a customer (requires email)
- `solvapay_get_customer` — Get by reference
- `solvapay_list_customers` — List/search with pagination
- `solvapay_update_customer` — Update name, email, telephone, metadata
- `solvapay_delete_customer` — Delete by reference
- `solvapay_ensure_customer` — Idempotent create-or-get by email

### Products
- `solvapay_create_product` — Create a product (requires name)
- `solvapay_get_product` — Get by reference
- `solvapay_list_products` — List/search products
- `solvapay_update_product` — Update name, description, categories
- `solvapay_delete_product` — Delete by reference

### Purchases
- `solvapay_list_purchases` — List all purchases
- `solvapay_get_purchase` — Get by ID
- `solvapay_get_purchases_by_customer` — By customer ID
- `solvapay_get_purchases_by_product` — By product ID
- `solvapay_cancel_renewal` — Cancel renewal with optional reason
- `solvapay_check_limits` — Check usage limits for customer/product pair

### Payments & Checkout
- `solvapay_create_checkout_session` — Create a Stripe checkout session
- `solvapay_create_customer_session` — Create a customer portal session

### Wallet & Revenue
- `solvapay_get_product_balance` — Revenue for a specific product

### Usage Tracking
- `solvapay_record_usage` — Record a single usage event
- `solvapay_record_bulk_usage` — Record multiple events at once

### Transactions & Refunds
- `solvapay_list_transactions` — List with date/status filters
- `solvapay_get_transaction` — Get by ID
- `solvapay_get_transaction_stats` — Transaction count and statistics
- `solvapay_create_refund` — Refund a transaction

## Common Workflows

**Onboard a new customer:**
1. `solvapay_ensure_customer` with their email
2. `solvapay_create_checkout_session` to generate a payment link

**Check business health:**
1. `solvapay_get_product_balance` for product revenue
2. `solvapay_get_transaction_stats` for transaction overview

**Investigate a customer issue:**
1. `solvapay_list_customers` to find them by search
2. `solvapay_get_purchases_by_customer` to see their purchases
3. `solvapay_list_transactions` to review payment history
4. `solvapay_create_refund` if a refund is needed

## Environment

- API keys starting with `sk_sandbox_` use test data (sandbox environment)
- API keys starting with `sk_live_` use real data (production environment)
- All operations are scoped to the authenticated provider account

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/solvapay) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
