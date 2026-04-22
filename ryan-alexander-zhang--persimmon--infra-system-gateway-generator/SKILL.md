---
name: infra-system-gateway-generator
description: Use when generating external system integration infrastructure (client/dto/gateway impl) organized by remote system name with error translation rules.
metadata:
  author: ryan-alexander-zhang
---

# Infra System Gateway Generator

## Overview
Generates system-first external integration infrastructure—client, DTOs, and gateway implementation—that bridges domain gateway ports to remote systems with proper error translation.
**REQUIRED:** Follow `GENERATOR_SKILL_STRUCTURE.md`. Variables in `VARIABLES.md`.

Templates: See `references/templates.md`.

## When to Use
- Integrating with an external system (HTTP/RPC/SDK) under:
  - `{{basePackage}}.infra.gateway.<system>.client`
  - `{{basePackage}}.infra.gateway.<system>.dto`
  - `{{basePackage}}.infra.gateway.<system>.impl`
- Implementing domain gateway ports (`{{basePackage}}.domain.{{bcName}}.gateway.*`)
- Adding a new remote system with its own client, DTOs, and error translation

### Don't use when
- Building internal BC-to-BC communication—use event-based integration via `infra-mq-transport-generator`
- Generating domain gateway port interfaces—use `domain-repository-port-generator` or define manually in domain
- Creating adapter-layer HTTP controllers—use `adapter-web-controller-generator`

## Inputs Required
- System name (`<system>`), e.g. `kubernetes`, `harbor`, `payment`
- Protocol (HTTP/RPC/SDK) and client choice
- Domain gateway port interface to implement
- Error model:
  - which failures are retryable vs non-retryable
  - what information must be preserved (codes, requestId, etc.)

## Outputs
- Client:
  - `.../infra/gateway/<system>/client/<XxxClient>.java`
- Integration DTOs:
  - `.../infra/gateway/<system>/dto/<XxxRequest>.java`
  - `.../infra/gateway/<system>/dto/<XxxResponse>.java`
- Gateway implementation:
  - `.../infra/gateway/<system>/impl/<XxxGatewayImpl>.java`
- Start wiring + config keys (if needed):
  - `.../start/config/bean/<System>GatewayWiringConfig.java`
  - YAML updates via `start-yaml-config-generator`

## Naming & Packaging
- System-first organization is mandatory (per `package-info.java`).
- Separate concerns:
  - `client`: protocol details (timeouts, retries, auth, serialization)
  - `dto`: integration request/response models
  - `impl`: domain port implementation and error translation

## Implementation Rules
- Domain-facing interface must stay business-oriented; do not leak protocol DTOs upward.
- Put retries/timeouts in `client` (policy belongs there), not in domain.
- Translate protocol errors in `impl` into domain/app meaningful failures.

## Reference Implementations
- Package rules:
  - `{{infraModuleDir}}/src/main/java/{{basePackagePath}}/infra/gateway/package-info.java`
  - `{{infraModuleDir}}/src/main/java/{{basePackagePath}}/infra/gateway/system/impl/package-info.java`

## Tests
- Unit tests for error translation rules are recommended.

## Common Mistakes
| Mistake | Why It Happens | Fix |
|---------|---------------|-----|
| Letting client exceptions propagate to app/domain | Missing catch in gateway impl | Translate all protocol exceptions in `impl` into domain-meaningful failures with retry classification |
| Mixing integration DTOs and domain models in the same package | Shortcut to reduce class count | Keep `dto` package for integration models; domain types stay in domain module |
| Putting retry/timeout config in gateway impl instead of client | Unclear responsibility separation | Retries, timeouts, and auth belong in `client`; `impl` handles error translation only |
| Not preserving error context (codes, requestId) in translations | Generic exception wrapping | Include upstream error codes, request IDs, and response details in translated domain exceptions |

## Phase Commit Gate

Before committing generated/updated code:
- Ensure compile and unit tests pass (minimum: `mvn -q clean test`).
- If DB behavior/schema changes are involved, run `mvn -q clean verify` to execute `*IT`.
- Invoke `requesting-code-review` for the intended commit scope and resolve Critical/Important findings.
- Prefer one focused commit per phase/slice (do not batch unrelated changes).

## Integration
- **Called by:** `scaffold-router`, `dev-workflow-ddd-implementation-workflow`
- **Pairs with:** `start-wiring-config-generator`, `start-yaml-config-generator`, `infra-it-http-generator`, `infra-it-k8s-generator`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryan-alexander-zhang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
