---
name: security-questionnaires
description: Enterprise security questionnaires are comprehensive surveys (100-500 Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Security Questionnaires

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Enterprise security questionnaires are comprehensive surveys (100-500 questions) from enterprise customers to assess vendor security practices. They are a critical blocker for enterprise sales and must be completed accurately and efficiently. This skill covers preparing for questionnaires, building standard response libraries, managing evidence, and streamlining the assessment process.

## Why This Matters
- **Sales Blocker**: Security questionnaires are required for enterprise deals; failure means lost revenue
- **Time Efficiency**: Standard response library reduces completion time from 20-40 hours to 5-10 hours
- **Consistency**: Ensures accurate, consistent responses across all customer questionnaires
- **Trust Building**: Professional, well-documented responses build customer confidence

---

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
  - Customer security questionnaire (Excel, Word, online form)
  - Evidence files (SOC2, ISO, pen test results)
  - Standard response library
  - Security policies and documentation
* **Entry Conditions**:
  - Security documentation prepared and up-to-date
  - Compliance certifications obtained (SOC2, ISO 27001)
  - Standard response library created with 200+ questions
  - Evidence files collected and organized
* **Outputs**:
  - Completed questionnaire with accurate responses
  - Evidence files attached where required
  - Cover letter summarizing security posture
* **Artifacts Required (Deliverables)**:
  - Standard response library (Google Doc/Notion)
  - Evidence checklist (all required files)
  - Trust center website (optional but recommended)
  - Questionnaire workflow documentation
* **Acceptance Evidence**:
  - Customer approval of questionnaire
  - No follow-up questions on major topics
  - Evidence files accepted by customer
* **Success Criteria**:
  - Questionnaire completed within 5-10 hours (with library)
  - Customer approval within 3-5 weeks
  - No major red flags raised
  - Evidence files accepted

## Skill Composition
* **Depends on**: SOC2 Type II Certification, ISO 27001 Certification, Security Policies
* **Compatible with**: Vendor Onboarding, SSO (SAML & OIDC), SCIM Provisioning
* **Conflicts with**: None
* **Related Skills**: [Vendor Onboarding](file://50-enterprise-integrations/vendor-onboarding/SKILL.md), [SSO (SAML & OIDC)](file://50-enterprise-integrations/sso-saml-oidc/SKILL.md)

---

## Quick Start / Implementation Example

1. Review requirements and constraints
2. Set up development environment
3. Implement core functionality following patterns
4. Write tests for critical paths
5. Run tests and fix issues
6. Document any deviations or decisions

```python
# Example implementation following best practices
def example_function():
    # Your implementation here
    pass
```


## Assumptions / Constraints / Non-goals

* **Assumptions**:
  - Development environment is properly configured
  - Required dependencies are available
  - Team has basic understanding of domain
* **Constraints**:
  - Must follow existing codebase conventions
  - Time and resource limitations
  - Compatibility requirements
* **Non-goals**:
  - This skill does not cover edge cases outside scope
  - Not a replacement for formal training


## Compatibility & Prerequisites

* **Supported Versions**:
  - Python 3.8+
  - Node.js 16+
  - Modern browsers (Chrome, Firefox, Safari, Edge)
* **Required AI Tools**:
  - Code editor (VS Code recommended)
  - Testing framework appropriate for language
  - Version control (Git)
* **Dependencies**:
  - Language-specific package manager
  - Build tools
  - Testing libraries
* **Environment Setup**:
  - `.env.example` keys: `API_KEY`, `DATABASE_URL` (no values)


## Test Scenario Matrix (QA Strategy)

| Type | Focus Area | Required Scenarios / Mocks |
| :--- | :--- | :--- |
| **Unit** | Core Logic | Must cover primary logic and at least 3 edge/error cases. Target minimum 80% coverage |
| **Integration** | DB / API | All external API calls or database connections must be mocked during unit tests |
| **E2E** | User Journey | Critical user flows to test |
| **Performance** | Latency / Load | Benchmark requirements |
| **Security** | Vuln / Auth | SAST/DAST or dependency audit |
| **Frontend** | UX / A11y | Accessibility checklist (WCAG), Performance Budget (Lighthouse score) |


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


## Agent Directives & Error Recovery
*(ข้อกำหนดสำหรับ AI Agent ในการคิดและแก้ปัญหาเมื่อเกิดข้อผิดพลาด)*

- **Thinking Process**: Analyze root cause before fixing. Do not brute-force.
- **Fallback Strategy**: Stop after 3 failed test attempts. Output root cause and ask for human intervention/clarification.
- **Self-Review**: Check against Guardrails & Anti-patterns before finalizing.
- **Output Constraints**: Output ONLY the modified code block. Do not explain unless asked.


## Definition of Done (DoD) Checklist

- [ ] Tests passed + coverage met
- [ ] Lint/Typecheck passed
- [ ] Logging/Metrics/Trace implemented
- [ ] Security checks passed
- [ ] Documentation/Changelog updated
- [ ] Accessibility/Performance requirements met (if frontend)


## Anti-patterns / Pitfalls

* ⛔ **Don't**: Log PII, catch-all exception, N+1 queries
* ⚠️ **Watch out for**: Common symptoms and quick fixes
* 💡 **Instead**: Use proper error handling, pagination, and logging


## Reference Links & Examples

* Internal documentation and examples
* Official documentation and best practices
* Community resources and discussions


## Versioning & Changelog

* **Version**: 1.0.0
* **Changelog**:
  - 2026-02-22: Initial version with complete template structure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
