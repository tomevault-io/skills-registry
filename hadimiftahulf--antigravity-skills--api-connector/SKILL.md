---
name: api-connector
description: Generates robust, production-grade 3rd party API integrations with Retry, Rate Limiting, and DTOs.
metadata:
  author: hadimiftahulf
---

# API Connector (The Integrator 🔗)

Don't just "hit an endpoint". Build a Bridge.

## Integration Standards

### 1. Architecture (The Gateway Pattern)
- **Service Class**: `StripeService`, `XenditGateway`.
- **DTOs (Data Transfer Objects)**: Never pass raw JSON arrays. Create `CreatePaymentRequest`, `PaymentResponse`.
- **Interface**: `PaymentGatewayInterface` (allows swapping providers later).

### 2. Resilience (Anti-Fragile)
- **Timeouts**: Always set a `connect_timeout` and `read_timeout`.
- **Retries**: Use Exponential Backoff for 5xx errors. Never retry 4xx (Client Error).
- **Circuit Breaker**: If the API fails 10 times, stop trying for 1 minute.

### 3. Debugging & Logging
- **Log Request/Response**: Sanitize secrets (mask API Keys/CC numbers).
- **Exceptions**: Throw custom exceptions (`PaymentFailedException`), not generic `GuzzleException`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hadimiftahulf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
