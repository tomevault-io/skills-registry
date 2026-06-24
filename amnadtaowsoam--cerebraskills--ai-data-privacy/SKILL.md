---
name: ai-data-privacy
description: AI Data Privacy is the practice of ensuring that personal and sensitive Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Ai Data Privacy

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
AI Data Privacy is the practice of ensuring that personal and sensitive data used to train and serve AI models is protected against unauthorized access, disclosure, or re-identification. Machine learning models can inadvertently "memorize" training data, creating unique privacy risks.

**Core Principle**: "The model should learn patterns, not people."

This skill provides comprehensive guidance on protecting data privacy throughout the AI/ML lifecycle.

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
- PII can be identified and removed
- Privacy techniques can be applied
- Team has capacity to implement privacy measures
- Compliance requirements are well-defined

## Compatibility
- Works with any ML framework
- Compatible with all data formats
- Framework-agnostic approach
- Adaptable to different privacy requirements

## Test Scenario Matrix
| Scenario | Test Case | Expected Outcome |
|----------|-----------|------------------|
| PII in training data | Remove direct identifiers | Anonymized data |
| Membership inference | Use differential privacy | Hard to infer membership |
| Model inversion | Add noise to outputs | Hard to reconstruct data |
| Data leakage | Scrub training data | No memorization |
| Unlearning request | Remove user influence | User data removed |

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
1. **Minimize data** - Only collect what's necessary
2. **Anonymize everything** - Remove PII before training
3. **Use privacy techniques** - Apply DP, federated learning, etc.
4. **Support unlearning** - Handle deletion requests
5. **Monitor for leaks** - Detect and prevent data leakage

## Definition of Done
AI data privacy implementation is complete when:

- [ ] Privacy risk assessment completed
- [ ] Data minimization implemented
- [ ] Anonymization techniques applied
- [ ] Privacy-preserving training implemented
- [ ] Unlearning procedures documented
- [ ] Prompt privacy measures in place
- [ ] Compliance frameworks followed
- [ ] Privacy documentation complete
- [ ] Team trained on privacy procedures
- [ ] Continuous monitoring in place

## Anti-patterns
1. **Training on raw PII** - Never train on identifiable data
2. **No anonymization** - Always remove PII before training
3. **Ignoring unlearning** - Must support deletion requests
4. **No monitoring** - Can't detect privacy violations
5. **Over-collecting** - Collect more data than needed

## Reference Links
#

## Versioning
This skill follows semantic versioning (MAJOR.MINOR.PATCH):
- **MAJOR**: Breaking changes to procedures or standards
- **MINOR**: New privacy methods or significant enhancements
- **PATCH**: Bug fixes or documentation updates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
