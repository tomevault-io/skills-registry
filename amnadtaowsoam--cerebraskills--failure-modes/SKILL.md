---
name: failure-modes
description: Failure Modes Analysis is a systematic approach to identifying, categorizing, Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Failure Modes

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Failure Modes Analysis is a systematic approach to identifying, categorizing, and mitigating potential failures in distributed systems. This skill teaches you how to anticipate system failures, understand their impact, and design resilient systems that gracefully handle various failure scenarios.

**Core Principle**: "Understand what can fail, so you can prevent it or handle it gracefully."

## Why This Matters
- **Proactive Risk Management**: Identify failures before they occur in production
- **Better Architecture**: Design systems that handle failures gracefully from the start
- **Faster Incident Response**: Pre-documented failure modes speed up root cause analysis
- **Improved Reliability**: Systematic approach reduces blind spots and assumptions

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
  - System architecture documentation
  - Component dependencies and relationships
  - Historical incident data
  - Performance metrics and baselines
* **Entry Conditions**:
  - System architecture is documented
  - Component boundaries are clearly defined
  - Monitoring and observability are in place
* **Outputs**:
  - Failure mode documentation (FMEA)
  - Risk priority matrix
  - Mitigation strategies for each failure mode
* **Artifacts Required (Deliverables)**:
  - FMEA spreadsheet with RPN calculations
  - Failure mode documentation for each component
  - Risk register with prioritized action items
* **Acceptance Evidence**:
  - Completed FMEA with all components analyzed
  - Risk register with mitigation owners
  - Test coverage for failure scenarios
* **Success Criteria**:
  - All critical components have documented failure modes
  - High-risk failure modes (RPN > 100) have mitigation plans
  - Team has reviewed and validated the analysis

## Skill Composition
* **Depends on**: System Architecture, Monitoring & Observability
* **Compatible with**: Chaos Engineering, Postmortem Analysis, Resilience Patterns
* **Conflicts with**: Systems without clear component boundaries
* **Related Skills**: 
  - [40-system-resilience/chaos-engineering](40-system-resilience/chaos-engineering/SKILL.md) - Proactive failure testing
  - [40-system-resilience/postmortem-analysis](40-system-resilience/postmortem-analysis/SKILL.md) - Learning from incidents
  - [40-system-resilience/bulkhead-patterns](40-system-resilience/bulkhead-patterns/SKILL.md) - Failure containment
  - [40-system-resilience/retry-timeout-strategies](40-system-resilience/retry-timeout-strategies/SKILL.md) - Handling transient failures

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
