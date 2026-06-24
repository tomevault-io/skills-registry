---
name: config-env-conventions
description: Service configuration and environment variable conventions across all Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Config Env Conventions

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Service configuration and environment variable conventions across all services: naming, validation, defaults, and secret handling for confident deployment across environments.

## Why This Matters
- **Portability**: Same code, different configs per env
- **Safety**: Validate config at startup, fail fast
- **Security**: Clear separation of secrets
- **Debugging**: Know where config comes from

## Core Concepts & Rules

### 1. Core Principles
- Follow established patterns and conventions
- Maintain consistency across codebase
- Document decisions and trade-offs

### 2. Implementation Guidelines
- Start with the simplest viable solution
- Iterate based on feedback and requirements
- Test thoroughly before deployment


## Inputs / Outputs / Contracts
* **Inputs**:
  - <e.g., env vars, request payload, file paths, schema>
* **Entry Conditions**:
  - <Pre-requisites: e.g., Repo initialized, DB running, specific branch checked out>
* **Outputs**:
  - <e.g., artifacts (PR diff, docs, tests, dashboard JSON)>
* **Artifacts Required (Deliverables)**:
  - <e.g., Code Diff, Unit Tests, Migration Script, API Docs>
* **Acceptance Evidence**:
  - <e.g., Test Report (screenshot/log), Benchmark Result, Security Scan Report>
* **Success Criteria**:
  - <e.g., p95 < 300ms, coverage ≥ 80%>

## Skill Composition
* **Depends on**: [secrets-key-management](../71-infrastructure-patterns/secrets-key-management/SKILL.md), [config-distribution](../69-platform-engineering-lite/config-distribution/SKILL.md)
* **Compatible with**: [api-style-guide](./api-style-guide/SKILL.md), [service-standards-blueprint](./service-standards-blueprint/SKILL.md)
* **Conflicts with**: None
* **Related Skills**: [env-matrix-dev-stg-prod](../69-platform-engineering-lite/env-matrix-dev-stg-prod/SKILL.md), [logging-metrics-tracing-standard](./logging-metrics-tracing-standard/SKILL.md)

## Quick Start
#

## Assumptions
- Services follow 12-Factor App principles
- Secret management system available (Vault, AWS SM, GCP SM)
- Multiple environments (development, staging, production)
- Configuration validation at startup
- TypeScript or similar language with schema validation

## Compatibility
- **Node.js**: 16+
- **TypeScript**: 4.5+
- **Zod**: 3.0+
- **Vault**: 1.10+
- **AWS Secrets Manager**: Latest API

## Test Scenario Matrix
| Scenario | Input | Expected Output | Verification |
|----------|-------|-----------------|--------------|
| Load valid config | Valid env vars | Config object loaded | No errors |
| Missing required var | Missing required env | Validation error | Error message |
| Invalid type | Invalid port string | Validation error | Type check |
| Secret loading | Secret from manager | Secret loaded securely | No logs of value |
| Environment detection | APP_ENV set | Correct environment | Env variable set |

## Technical Guardrails
#

## Agent Directives & Error Recovery
*(ข้อกำหนดสำหรับ AI Agent ในการคิดและแก้ปัญหาเมื่อเกิดข้อผิดพลาด)*

- **Thinking Process**: Analyze root cause before fixing. Do not brute-force.
- **Fallback Strategy**: Stop after 3 failed test attempts. Output root cause and ask for human intervention/clarification.
- **Self-Review**: Check against Guardrails & Anti-patterns before finalizing.
- **Output Constraints**: Output ONLY the modified code block. Do not explain unless asked.


## Definition of Done
Configuration is complete when:

- [ ] All env vars follow naming convention
- [ ] Configuration schema defined and validated
- [ ] `.env.example` complete with all keys
- [ ] Required vs optional clearly marked
- [ ] Secrets not in `.env` files
- [ ] Defaults are safe (not production values)
- [ ] Environment detection implemented
- [ ] Config changes don't require code changes
- [ ] Documentation complete
- [ ] Startup validation tested

## Anti-patterns
1. **Hardcoded values**: Config in code
2. **Secrets in .env**: Committed to git
3. **No validation**: Runtime failures
4. **Magic strings**: Config keys scattered in code
5. **Implicit env**: Behavior changes based on `NODE_ENV` unpredictably
6. **Production defaults**: Real production values as defaults
7. **Mixed secrets**: Secrets and config in same place
8. **No documentation**: Undocumented configuration options

## Reference Links
- [12-Factor App: Config](https://12factor.net/config)
- [Node.js Config Best Practices](https://github.com/goldbergyoni/nodebestpractices#1-project-structure-practices)
- [Zod Documentation](https://zod.dev/)
- [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/)

## Versioning & Changelog

* **Version**: 1.0.0
* **Changelog**:
  - 2026-02-22: Initial version with complete template structure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
