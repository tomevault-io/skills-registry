---
name: payments
description: > Use when this capability is needed.
metadata:
  author: sailscastshq
---

# Payments

The Boring JavaScript Stack uses `sails-pay` with Lemon Squeezy as the payment provider. The Ascent templates include a complete billing system with plan-based subscriptions, checkout flows, webhook handling, and customer portal integration.

## When to Use

Use this skill when:

- Configuring `sails-pay` in `config/pay.js` (provider, plans, variants)
- Implementing checkout flows with `sails.pay.checkout()`
- Managing subscriptions (Subscription model, status lifecycle)
- Handling Lemon Squeezy webhooks (signature verification, event processing)
- Building billing settings pages with plan information
- Checking subscription status with the `checkPlan` helper
- Integrating Lemon Squeezy's hosted customer portal

## Rules

Read individual rule files for detailed explanations and code examples:

- [rules/getting-started.md](rules/getting-started.md) - sails-pay hook, config/pay.js, Lemon Squeezy setup, Subscription model
- [rules/checkout.md](rules/checkout.md) - Checkout flow, sails.pay.checkout(), variant selection, custom data
- [rules/subscriptions.md](rules/subscriptions.md) - Subscription model, status management, checkPlan helper, billing page
- [rules/webhooks.md](rules/webhooks.md) - Webhook controller, signature verification, event handling, idempotency
- [rules/customer-portal.md](rules/customer-portal.md) - Lemon Squeezy hosted portal, subscription management URLs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sailscastshq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
