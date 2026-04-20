---
name: structured-logging
description: Best practices for production logging — structured data, wide events, high cardinality, and queryable logs. Use when implementing logging, debugging production issues, designing observability, or when asked "why can't I find anything in the logs? Use when this capability is needed.
metadata:
  author: oxfrancesco
---

# Structured Logging

Modern logging practices for production systems. Stop grep-ing, start querying.

## The Problem

Traditional logging is broken:
- **17 log lines** for a single request = noise, not signal
- **String search** can't correlate events across services
- **Low context** = hours debugging at 2am
- **Optimized for writing**, not querying

**Example bad log:**
```
2024-12-20T03:14:24.312Z ERROR Database connection pool exhausted active_connections=20
```

What user? What request? What service? What's the impact?

## The Solution: Wide Events

**One log line per request per service** with all the context.

**Example wide event:**
```json
{
  "timestamp": "2025-01-15T10:23:45.612Z",
  "request_id": "req_8bf7ec2d",
  "trace_id": "abc123",

  "service": "checkout-service",
  "version": "2.4.1",
  "region": "us-east-1",

  "method": "POST",
  "path": "/api/checkout",
  "status_code": 500,
  "duration_ms": 1247,

  "user": {
    "id": "user_456",
    "subscription": "premium",
    "account_age_days": 847,
    "lifetime_value_cents": 284700
  },

  "cart": {
    "id": "cart_xyz",
    "item_count": 3,
    "total_cents": 15999,
    "coupon_applied": "SAVE20"
  },

  "payment": {
    "method": "card",
    "provider": "stripe",
    "latency_ms": 1089,
    "attempt": 3
  },

  "error": {
    "type": "PaymentError",
    "code": "card_declined",
    "message": "Card declined by issuer",
    "retriable": false
  }
}
```

One event. Everything you need. Instant debugging.

## Core Concepts

### 1. Structured Logging

**❌ Don't:**
```javascript
console.log("Payment failed for user 123");
```

**✅ Do:**
```javascript
logger.info({
  event: "payment_failed",
  user_id: "user_123",
  amount: 15999,
  error: "card_declined"
});
```

### 2. High Cardinality

Fields with many unique values make logs **searchable**:
- `user_id` — millions of values ✅
- `request_id` — unique per request ✅
- `subscription_tier` — 3 values (free, pro, enterprise) ❌

**Include high-cardinality fields:**
- user_id, order_id, session_id
- request_id, trace_id
- cart_id, payment_id

### 3. High Dimensionality

More fields = more questions you can answer:
- 5 fields → "Did user X fail?"
- 50 fields → "Did premium users on mobile with new checkout flow fail more?"

**Log everything that might matter:**
- User context (id, tier, account_age, lifetime_value)
- Request context (method, path, user_agent, ip)
- Business context (cart value, coupon, feature_flags)
- Performance (duration_ms, db_queries, cache_hits)
- Errors (type, code, retriable, stack_trace)

### 4. Wide Events / Canonical Log Lines

**Pattern:** Build event throughout request → emit once at end

```typescript
// Middleware
export function wideEventMiddleware() {
  return async (ctx, next) => {
    const startTime = Date.now();

    // Initialize event
    const event = {
      request_id: ctx.get('requestId'),
      timestamp: new Date().toISOString(),
      method: ctx.req.method,
      path: ctx.req.path,
      service: process.env.SERVICE_NAME,
      version: process.env.SERVICE_VERSION,
    };

    ctx.set('wideEvent', event);

    try {
      await next();
      event.status_code = ctx.res.status;
      event.outcome = 'success';
    } catch (error) {
      event.status_code = 500;
      event.outcome = 'error';
      event.error = {
        type: error.name,
        message: error.message,
        code: error.code,
        retriable: error.retriable ?? false,
      };
      throw error;
    } finally {
      event.duration_ms = Date.now() - startTime;

      // Emit once at end
      logger.info(event);
    }
  };
}
```

**Then enrich in handlers:**
```typescript
app.post('/checkout', async (ctx) => {
  const event = ctx.get('wideEvent');
  const user = ctx.get('user');

  // Add context
  event.user = {
    id: user.id,
    subscription: user.tier,
    account_age_days: getAccountAge(user),
    lifetime_value_cents: user.ltv,
  };

  event.cart = {
    id: ctx.cart.id,
    item_count: ctx.cart.items.length,
    total_cents: ctx.cart.total,
    coupon_applied: ctx.cart.coupon,
  };

  // ... handle request
});
```

## What This Enables

Instead of grep-ing text, query structured data:

**"Show me premium users who failed checkout in the last hour"**
```sql
WHERE user.subscription = 'premium'
  AND outcome = 'error'
  AND timestamp > NOW() - INTERVAL 1 HOUR
```

**"Did the new checkout flow increase failures?"**
```sql
SELECT feature_flags.new_checkout_flow, COUNT(*), AVG(duration_ms)
WHERE outcome = 'error'
GROUP BY feature_flags.new_checkout_flow
```

**"Which payment provider is failing most?"**
```sql
SELECT payment.provider, error.code, COUNT(*)
WHERE outcome = 'error'
GROUP BY payment.provider, error.code
ORDER BY COUNT(*) DESC
```

## Anti-Patterns

**❌ Logging what code is doing**
```
"Processing payment"
"Calling Stripe API"
"Payment processed"
```

**✅ Logging what happened to the request**
```json
{
  "event": "payment_completed",
  "user_id": "user_456",
  "payment_provider": "stripe",
  "amount_cents": 15999,
  "duration_ms": 1089,
  "outcome": "success"
}
```

**❌ Low cardinality only**
```json
{
  "level": "ERROR",
  "service": "api",
  "environment": "production"
}
```
This tells you nothing useful.

**✅ High cardinality + high dimensionality**
```json
{
  "user_id": "user_456",
  "request_id": "req_8bf7ec2d",
  "subscription_tier": "premium",
  "cart_value_cents": 15999,
  "payment_method": "card",
  "error_code": "insufficient_funds"
}
```

## Implementation Checklist

**When adding logging to a service:**

- [ ] Use structured logging (JSON, key-value pairs)
- [ ] Create wide event middleware
- [ ] Add high-cardinality fields (user_id, request_id)
- [ ] Add business context (tier, cart value, flags)
- [ ] Emit once per request (not 17 times)
- [ ] Include error details (type, code, retriable)
- [ ] Add performance metrics (duration_ms, db_time)
- [ ] Test queries: "Can I find user X's failed request?"

## Tools & Libraries

**Node.js:**
- pino (fast structured logging)
- winston (feature-rich)
- console-log-json (drop-in replacement)

**Python:**
- structlog (structured logging)
- python-json-logger (JSON formatter)

**Go:**
- zap (uber's logging library)
- zerolog (zero-allocation JSON)

**All:**
- OpenTelemetry (protocol/SDKs, not a silver bullet)

## References

See [references/wide-events-guide.md](references/wide-events-guide.md) for:
- Complete implementation examples
- Query patterns
- Common mistakes
- Migration guide from legacy logs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oxfrancesco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
