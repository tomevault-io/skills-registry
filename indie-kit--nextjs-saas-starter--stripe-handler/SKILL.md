---
name: stripe-handler
description: Handle Stripe payments, webhooks, and subscriptions. Use when this capability is needed.
metadata:
  author: indie-kit
---

# Stripe Handler

## Instructions
- Use `src/lib/stripe.ts` for Stripe instance.
- Webhooks are handled in `src/app/api/webhooks/stripe/route.ts` (Scaffold provided).
- Implement your logic in the `StripeWebhookHandler` class in the route file.
- Use `stripe` CLI for testing webhooks locally.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/indie-kit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
