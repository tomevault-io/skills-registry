---
name: event-style-guide
description: Event-driven design conventions: event envelope, naming, versioning, Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Event Style Guide

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Event-driven design conventions: event envelope, naming, versioning, and schema evolution rules that enable safe system decoupling.

## Why This Matters
- **Interoperability**: All services understand same event format
- **Evolution**: Change schema without breaking consumers
- **Debugging**: Trace events across services
- **Contract**: Clear agreement between producers/consumers

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
* **Depends on**: [api-style-guide](./api-style-guide/SKILL.md), [logging-metrics-tracing-standard](./logging-metrics-tracing-standard/SKILL.md)
* **Compatible with**: [service-standards-blueprint](./service-standards-blueprint/SKILL.md), [messaging-queue](../../08-messaging-queue/)
* **Conflicts with**: None
* **Related Skills**: [event-contract-generator](../67-codegen-scaffolding-automation/event-contract-generator/SKILL.md), [distributed-tracing](../../14-monitoring-observability/distributed-tracing/SKILL.md)

## Quick Start
#

## Assumptions
- Event-driven architecture in use
- Message broker available (Kafka, RabbitMQ, etc.)
- Schema registry for event definitions
- Multiple services producing/consuming events
- Need for schema evolution without breaking changes

## Compatibility
- **CloudEvents**: 1.0 spec
- **Kafka**: 2.8+
- **RabbitMQ**: 3.9+
- **Schema Registry**: Confluent 7.0+
- **TypeScript**: 4.5+

## Test Scenario Matrix
| Scenario | Input | Expected Output | Verification |
|----------|-------|-----------------|--------------|
| Create event | Domain event data | Valid event envelope | Schema validation |
| Version change | Breaking schema change | New version event | Version in type |
| Consumer receives | Event from broker | Processed idempotently | No duplicate processing |
| Schema evolution | New optional field | Old consumers still work | Backward compatibility |
| Retry failure | Non-retryable error | Sent to DLQ | DLQ inspection |

## Technical Guardrails
#

## Agent Directives & Error Recovery
*(ข้อกำหนดสำหรับ AI Agent ในการคิดและแก้ปัญหาเมื่อเกิดข้อผิดพลาด)*

- **Thinking Process**: Analyze root cause before fixing. Do not brute-force.
- **Fallback Strategy**: Stop after 3 failed test attempts. Output root cause and ask for human intervention/clarification.
- **Self-Review**: Check against Guardrails & Anti-patterns before finalizing.
- **Output Constraints**: Output ONLY the modified code block. Do not explain unless asked.


## Definition of Done
An event is complete when:

- [ ] Event follows envelope standard
- [ ] Event naming follows convention
- [ ] Version included in event type
- [ ] Schema registered in registry
- [ ] Correlation ID present
- [ ] Unique event ID (UUID v4)
- [ ] Consumer implements idempotency
- [ ] Dead letter handling configured
- [ ] Documentation complete
- [ ] Examples provided

## Anti-patterns
1. **No versioning**: Can't evolve schemas
2. **Massive payloads**: Events too large
3. **Missing correlation**: Can't trace flows
4. **Shared events**: Tight coupling between services
5. **Hidden contracts**: Schema in code but no registry/docs
6. **No idempotency**: Duplicate processing causes issues
7. **Breaking changes without version**: Breaks consumers
8. **Mixed metadata**: Metadata in payload instead of envelope

## Reference Links
- [CloudEvents Specification](https://cloudevents.io/)
- [Event-Driven Architecture Patterns](https://www.confluent.io/blog/)
- [Schema Evolution Best Practices](https://docs.confluent.io/platform/current/schema-registry/)
- [Kafka Documentation](https://kafka.apache.org/documentation/)
- [RabbitMQ Tutorials](https://www.rabbitmq.com/tutorials.html)

## Versioning & Changelog

* **Version**: 1.0.0
* **Changelog**:
  - 2026-02-22: Initial version with complete template structure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
