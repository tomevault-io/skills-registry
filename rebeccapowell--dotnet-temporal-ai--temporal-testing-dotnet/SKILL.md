---
name: temporal-testing-dotnet
description: Testing strategies for Temporal .NET workflows and activities, emphasizing time-skipping and deterministic assertions. Use when this capability is needed.
metadata:
  author: rebeccapowell
---

# Testing Temporal .NET (Time Skipping)

## When to use
Use when asked:
- “how do I test this workflow?”
- “my tests are slow/flaky”
- “can I use WebApplicationFactory?” (answer: not for workflow unit tests)

## Required output
- Test pyramid: workflow tests vs activity tests vs integration tests
- Example workflow test using time-skipping (no real delays)
- What to assert (behavior) vs what not to assert (implementation details)

## Guardrails
- Never use real timers for workflow unit tests.
- Activity tests can be normal .NET unit tests where possible.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rebeccapowell) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
