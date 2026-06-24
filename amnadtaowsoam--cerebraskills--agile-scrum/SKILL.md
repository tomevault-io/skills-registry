---
name: agile-scrum
description: Agile Scrum is an iterative framework for managing complex projects through Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Agile Scrum

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Agile Scrum is an iterative framework for managing complex projects through short development cycles called sprints. This skill covers Scrum roles (Product Owner, Scrum Master, Development Team), ceremonies (Sprint Planning, Daily Standup, Sprint Review, Retrospective), artifacts (Product Backlog, Sprint Backlog, Increment), and best practices for effective team collaboration and continuous delivery.

## Why This Matters
- **Faster Time to Market**: Deliver working software in 2-4 week sprints instead of 6-12 month cycles
- **Higher Quality**: Continuous testing and integration reduce defects
- **Better Adaptability**: Respond quickly to changing requirements and market conditions
- **Improved Collaboration**: Cross-functional teams work together with clear roles and responsibilities
- **Continuous Improvement**: Retrospectives drive process improvements each sprint

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
  - Product requirements and user stories
  - Team capacity and velocity data
  - Previous sprint retrospectives
  - Stakeholder feedback
* **Entry Conditions**:
  - Scrum team formed with assigned roles
  - Initial product backlog created
  - Sprint duration established (typically 2 weeks)
  - Project management tool configured (Jira, Trello, etc.)
* **Outputs**:
  - Sprint backlog with committed stories
  - Working software increment
  - Sprint review demo presentation
  - Retrospective action items
  - Updated product backlog
* **Artifacts Required (Deliverables)**:
  - Product Backlog document
  - Sprint Backlog for each sprint
  - Sprint Review slides/demo
  - Retrospective notes and action items
  - Velocity charts and burndown charts
* **Acceptance Evidence**:
  - Completed sprint review with stakeholder sign-off
  - All sprint stories meeting Definition of Done
  - Retrospective action items documented
  - Updated velocity metrics
* **Success Criteria**:
  - Sprint goal achieved (80%+ completion rate)
  - Stable velocity (±20% over 3 sprints)
  - Team satisfaction score > 7/10
  - Stakeholder feedback positive

## Skill Composition
* **Depends on**: [user-stories](../18-project-management/user-stories/SKILL.md), [requirement-analysis](../18-project-management/requirement-analysis/SKILL.md)
* **Compatible with**: [estimation-techniques](../18-project-management/estimation-techniques/SKILL.md), [project-planning](../18-project-management/project-planning/SKILL.md), [risk-management](../18-project-management/risk-management/SKILL.md)
* **Conflicts with**: Waterfall methodology, rigid project management approaches
* **Related Skills**: [technical-specifications](../18-project-management/technical-specifications/SKILL.md), [ci-cd-github-actions](../15-devops-infrastructure/ci-cd-github-actions/SKILL.md)

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
