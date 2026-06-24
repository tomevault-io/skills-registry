---
name: polar-integration-validator
description: Autonomous validation of Polar.sh billing integration. Checks webhook endpoints, signature verification, subscription middleware, and environment configuration. Use when this capability is needed.
metadata:
  author: hirefrank
---

# Polar Integration Validator SKILL

## Activation Patterns

This SKILL automatically activates when:
- Files matching `**/webhooks/polar.*` are created/modified
- Files containing "subscription" or "polar" in path are modified
- `wrangler.toml` is updated
- Environment variable files (`.dev.vars`, `.env`) are modified
- Before deployment operations

## Validation Rules

### P1 - Critical (Block Operations)

**Webhook Endpoint**:
- ✅ Webhook handler exists (`server/api/webhooks/polar.ts` or similar)
- ✅ Signature verification implemented (`polar.webhooks.verify`)
- ✅ All critical events handled: `checkout.completed`, `subscription.created`, `subscription.updated`, `subscription.canceled`

**Environment Variables**:
- ✅ `POLAR_ACCESS_TOKEN` configured (check `.dev.vars` or secrets)
- ✅ `POLAR_WEBHOOK_SECRET` in wrangler.toml

**Database**:
- ✅ Users table has `polar_customer_id` column
- ✅ Subscriptions table exists
- ✅ Foreign key relationship configured

### P2 - Important (Warn)

**Event Handling**:
- ⚠️ `subscription.past_due` handler exists
- ⚠️ Database updates in all event handlers
- ⚠️ Error logging implemented

**Subscription Middleware**:
- ⚠️ Subscription check function exists
- ⚠️ Used on protected routes
- ⚠️ Checks `subscription_status === 'active'`
- ⚠️ Checks `current_period_end` not expired

### P3 - Suggestions (Inform)

- ℹ️ Webhook event logging to database
- ℹ️ Customer creation helper function
- ℹ️ Subscription status caching
- ℹ️ Rate limiting on webhook endpoint

## Validation Output

```
🔍 Polar.sh Integration Validation

✅ P1 Checks (Critical):
   ✅ Webhook endpoint exists
   ✅ Signature verification implemented
   ✅ Environment variables configured
   ✅ Database schema complete

⚠️ P2 Checks (Important):
   ⚠️ Missing subscription.past_due handler
   ✅ Subscription middleware exists
   ✅ Protected routes check subscription

ℹ️ P3 Suggestions:
   ℹ️ Consider adding webhook event logging
   ℹ️ Add rate limiting to webhook endpoint

📋 Summary: 1 warning found
💡 Run /es-billing-setup to fix issues
```

## Escalation

Complex scenarios escalate to `polar-billing-specialist` agent:
- Custom webhook processing logic
- Multi-tenant subscription architecture
- Usage-based billing implementation
- Migration from other billing providers

## Notes

- Runs automatically on relevant file changes
- Can block deployments with P1 issues
- Queries Polar MCP for product validation
- Integrates with `/validate` and `/es-deploy` commands

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hirefrank) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
