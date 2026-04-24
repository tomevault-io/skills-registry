---
name: contract-testing-generator
description: Generates contract tests (e.g. Pact) for API consumer and provider. Use for external API integration or microservice boundaries.
metadata:
  author: mouayadakel
---

# Contract Testing Generator

## When to Trigger

- External API integration
- Microservices communication
- "Test API contracts"

## What to Do

1. **Consumer**: Define expected request/response for each endpoint; run tests against provider mock; publish pact file.
2. **Provider**: Verify provider against pact(s); state handlers to set up data for each interaction.
3. **Contract**: Document endpoint, method, headers, body shape, status; keep in sync with real API.
4. **CI**: Run consumer tests then provider verification; fail if contract broken.

Use jest-pact or Pact JS; store pacts in repo or pact broker. Ensures frontend/consumers and backend stay compatible.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mouayadakel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
