---
name: auditability
description: AI Auditability ensures that all AI decisions are logged, traceable, Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Auditability

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
AI Auditability ensures that all AI decisions are logged, traceable, and explainable. This is critical for regulatory compliance, debugging, bias detection, and incident investigation.

**Core Principle**: "If it's not logged, it didn't happen. Every AI decision must be auditable."

This skill provides comprehensive guidance on implementing auditability across AI systems.

## Why This Matters
- **<Benefit>**: <short explanation>
- **<Benefit>**: <short explanation>
- **<Benefit>**: <short explanation>

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
* **Depends on**: None
* **Compatible with**: None
* **Conflicts with**: None
* **Related Skills**: None

## Quick Start
#

## Assumptions
- AI decisions can be logged with metadata
- Storage backend is available and scalable
- Team has capacity to review audit logs
- Compliance requirements are well-defined
- Query patterns are predictable

## Compatibility
- Works with any AI system
- Compatible with all logging frameworks
- Framework-agnostic approach
- Adaptable to different storage backends

## Test Scenario Matrix
| Scenario | Test Case | Expected Outcome |
|----------|-----------|------------------|
| Decision logged | AI prediction recorded | Queryable in audit logs |
| User queries | Retrieve user decisions | Complete audit trail returned |
| PII anonymized | Sensitive data hashed | Protected in logs |
| Compliance check | Audit passes requirements | System compliant |

## Technical Guardrails & Security Threat Model

### 1. Security & Privacy (Threat Model)
* **Top Threats**: Injection attacks, authentication bypass, data exposure
- [ ] **Data Handling**: Sanitize all user inputs to prevent Injection attacks. Never log raw PII
- [ ] **Secrets Management**: No hardcoded API keys. Use Env Vars/Secrets Manager
- [ ] **Authorization**: Validate user permissions before state changes

### 2. Performance & Resources
- [ ] **Execution Efficiency**: Consider time complexity for algorithms
- [ ] **Memory Management**: Use streams/pagination for large data
- [ ] **Resource Cleanup**: Close DB connections/file handlers in finally blocks

### 3. Architecture & Scalability
- [ ] **Design Pattern**: Follow SOLID principles, use Dependency Injection
- [ ] **Modularity**: Decouple logic from UI/Frameworks

### 4. Observability & Reliability
- [ ] **Logging Standards**: Structured JSON, include trace IDs `request_id`
- [ ] **Metrics**: Track `error_rate`, `latency`, `queue_depth`
- [ ] **Error Handling**: Standardized error codes, no bare except
- [ ] **Observability Artifacts**:
    - **Log Fields**: timestamp, level, message, request_id
    - **Metrics**: request_count, error_count, response_time
    - **Dashboards/Alerts**: High Error Rate > 5%


## Agent Directives
1. **Log everything** - Never skip logging AI decisions
2. **Anonymize PII** - Always protect sensitive data
3. **Use structured format** - Queryable, consistent schema
4. **Explain decisions** - Document reasoning for predictions
5. **Maintain traceability** - Chain of custody for all data
6. **Immutable logs** - Never modify audit records

## Definition of Done
AI auditability implementation is complete when:

- [ ] Audit log schema defined and implemented
- [ ] Audit logger integrated with AI systems
- [ ] Query interface implemented and documented
- [ ] PII anonymization in place for logs
- [ ] Compliance reporting automated
- [ ] Bias detection implemented in audit analysis
- [ ] Audit retention policy defined
- [ ] Team trained on audit procedures
- [ ] Monitoring in place for audit system health
- [ ] Documentation complete and up-to-date

## Anti-patterns
1. **Selective logging** - Log only important decisions
2. **Unstructured logs** - Free-form text without schema
3. **Missing context** - Not logging input features
4. **No PII protection** - Sensitive data in plain text
5. **No query interface** - Cannot search audit logs
6. **Short retention** - Logs deleted before analysis

## Reference Links
#

## Versioning
This skill follows semantic versioning (MAJOR.MINOR.PATCH):
- **MAJOR**: Breaking changes to procedures or standards
- **MINOR**: New audit methods or significant enhancements
- **PATCH**: Bug fixes or documentation updates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
