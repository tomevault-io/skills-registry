---
name: stripe
description: Stripe payment platform integration. Manage payments, subscriptions, invoices, and customers via Stripe API. Use when this capability is needed.
metadata:
  author: sundial-org
---

# Stripe 💵

Stripe payment platform integration.

## Setup

```bash
export STRIPE_API_KEY="sk_live_..."
```

## Features

- Create payment intents
- Manage subscriptions
- Send invoices
- Customer management
- Refund processing
- Webhook handling

## Usage Examples

```
"Create a $50 payment link"
"List recent Stripe payments"
"Refund payment pi_xxx"
"Show subscription for customer@email.com"
```

## API Reference

```bash
# List recent charges
curl -s https://api.stripe.com/v1/charges?limit=10 \
  -u "$STRIPE_API_KEY:"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
