---
name: service-standards-blueprint
description: Single Source of Truth (SSOT) for service creation standards that ensure Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Service Standards Blueprint

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Single Source of Truth (SSOT) for service creation standards that ensure consistent implementation across all services by both AI and humans. Covers structure, naming, dependencies, and required patterns.

## Why This Matters
- **Consistency**: Every service looks same and is easy to understand
- **Onboarding**: New team members and AI understand codebase quickly
- **Maintainability**: Fix once, apply everywhere
- **Quality**: One standard equals consistent quality

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
* **Depends on**: [api-style-guide](./api-style-guide/SKILL.md), [config-env-conventions](./config-env-conventions/SKILL.md)
* **Compatible with**: [logging-metrics-tracing-standard](./logging-metrics-tracing-standard/SKILL.md), [security-baseline-controls](./security-baseline-controls/SKILL.md)
* **Conflicts with**: None
* **Related Skills**: [definition-of-done](../68-quality-gates-ci-policies/definition-of-done/SKILL.md), [service-scaffold-generator](../67-codegen-scaffolding-automation/service-scaffold-generator/SKILL.md)

## Quick Start
#

## Assumptions
- Microservices architecture in use
- Multiple services being developed
- Need for consistency across services
- Standard technology stack
- CI/CD pipelines in place

## Compatibility
- **Node.js**: 16+
- **TypeScript**: 4.5+
- **Docker**: Latest
- **Kubernetes**: 1.20+
- **Testing frameworks**: Jest, Mocha, etc.

## Test Scenario Matrix
| Scenario | Input | Expected Output | Verification |
|----------|-------|-----------------|--------------|
| Create service | Blueprint config | Standard service structure | Folder structure |
| Health check | GET /healthz | 200 OK | Response status |
| Ready check | GET /readyz | 200 OK (if deps ready) | Response status |
| Metrics | GET /metrics | Prometheus format | Metric format |
| Config load | Environment vars | Validated config | No errors |

## Technical Guardrails
#

## Agent Directives & Error Recovery
*(ข้อกำหนดสำหรับ AI Agent ในการคิดและแก้ปัญหาเมื่อเกิดข้อผิดพลาด)*

- **Thinking Process**: Analyze root cause before fixing. Do not brute-force.
- **Fallback Strategy**: Stop after 3 failed test attempts. Output root cause and ask for human intervention/clarification.
- **Self-Review**: Check against Guardrails & Anti-patterns before finalizing.
- **Output Constraints**: Output ONLY the modified code block. Do not explain unless asked.


## Definition of Done
A service is complete when:

- [ ] Service follows folder structure standard
- [ ] All required endpoints implemented (health, ready, metrics)
- [ ] Config follows convention
- [ ] Logging format matches standard
- [ ] Tests meet coverage threshold
- [ ] Documentation complete
- [ ] Error shape + codes documented (catalog)
- [ ] Dashboards/alerts exist for SLOs
- [ ] Runbook exists for top alerts
- [ ] Security baseline implemented

## Anti-patterns
1. **Snowflake services**: Every service is completely different
2. **Copy-paste drift**: Copy then modify until they diverge
3. **Undocumented exceptions**: Do things differently without explaining why
4. **Version sprawl**: Different dependency versions
5. **No ownership**: No owner for service/alerts/runbooks

## Reference Links
- [12-Factor App](https://12factor.net/)
- [Microservices Patterns](https://microservices.io/patterns/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)

## Versioning & Changelog

* **Version**: 1.0.0
* **Changelog**:
  - 2026-02-22: Initial version with complete template structure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
