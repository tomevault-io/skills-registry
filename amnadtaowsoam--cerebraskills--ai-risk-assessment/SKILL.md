---
name: ai-risk-assessment
description: AI Risk Assessment is the systematic process of identifying potential Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Ai Risk Assessment

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
AI Risk Assessment is the systematic process of identifying potential harms from AI systems, evaluating their likelihood and impact, and implementing mitigations. This is essential for responsible AI deployment and regulatory compliance.

**Core Principle**: "Identify risks before they become incidents. Prevention is cheaper than remediation."

This skill provides comprehensive guidance on assessing and mitigating AI risks across safety, privacy, security, and ethics dimensions.

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
- Risk categories are well-defined
- Team has capacity to conduct assessments
- Use cases are clearly documented
- Regulatory requirements are known

## Compatibility
- Works with any AI system
- Compatible with all ML frameworks
- Framework-agnostic approach
- Adaptable to different risk categories

## Test Scenario Matrix
| Scenario | Test Case | Expected Outcome |
|----------|-----------|------------------|
| Safety risk | Autonomous vehicle decision | Risk identified, mitigation in place |
| Bias detection | Disparate impact across groups | Bias metrics measured, thresholds set |
| Privacy risk | PII in training data | Data anonymized before training |
| Security risk | Adversarial attack | Robustness score calculated, defenses implemented |
| Ethical risk | Harmful content | Content filters in place, human review |

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
1. **Assess before deploy** - Never skip risk evaluation
2. **Use risk matrix** - Prioritize by likelihood × impact
3. **Implement guardrails** - Technical and process controls
4. **Test for vulnerabilities** - Red teaming and adversarial testing
5. **Document everything** - Maintain complete risk records

## Definition of Done
AI risk assessment implementation is complete when:

- [ ] Risk categories defined for all systems
- [ ] Risk assessment framework implemented
- [ ] Risk register established
- [ ] Mitigation strategies documented
- [ ] Monitoring in place for risks
- [ ] Team trained on assessment procedures
- [ ] Compliance requirements met
- [ ] Documentation complete and up-to-date
- [ ] Continuous review process established

## Anti-patterns
1. **Deploying without assessment** - Building AI without risk evaluation
2. **Ignoring high risks** - Not addressing critical vulnerabilities
3. **No monitoring** - Can't detect new risks
4. **Insufficient testing** - Not testing for vulnerabilities
5. **Poor documentation** - Incomplete risk records

## Reference Links
#

## Versioning
This skill follows semantic versioning (MAJOR.MINOR.PATCH):
- **MAJOR**: Breaking changes to procedures or standards
- **MINOR**: New assessment methods or significant enhancements
- **PATCH**: Bug fixes or documentation updates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
