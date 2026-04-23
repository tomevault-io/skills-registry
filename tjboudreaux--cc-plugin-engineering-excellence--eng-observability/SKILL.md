---
name: eng-observability
description: Design every change with traceability, diagnostics, and fast incident triage in mind across mobile, web, and web3 stacks. Use when this capability is needed.
metadata:
  author: tjboudreaux
---

# Observability and Debugging Discipline

## Intent
- Make it trivial to answer “what is happening” and “why” without attaching a debugger in production.
- Ensure logs, metrics, events, and traces capture user intent, environment, and failure context while protecting sensitive data.

## Guiding Principles
1. Prefer structured logs + correlation IDs over ad-hoc strings.
2. Emit signals at every boundary (client, API, worker, contract invocation).
3. Include context (user/session/network/chain) necessary to reproduce issues.
4. Keep signal cost reasonable—throttle chatty paths, sample intelligently.
5. Build fast local debugging loops (trace replay, state inspectors, dev wallets).

## Workflow
1. Identify critical paths affected and define success/error signals per path.
2. Add/extend tracing spans or log blocks with consistent field names.
3. Validate observability locally by simulating successes, errors, and timeouts; ensure signals reach the sink (console, APM, analytics, chain explorer).
4. Document dashboards, queries, or CLI commands useful for post-deploy verification.
5. For on-chain logic, emit events with canonical schema so downstream indexers can consume them.

## Verification
- Run the code with verbose logging/tracing enabled; inspect outputs for clarity and privacy.
- Confirm metrics/counters appear where expected (APM, telemetry pipeline, analytics, chain explorer).
- Dry-run incident response: can you locate a test failure or simulated outage using only emitted signals? If not, iterate.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tjboudreaux) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
