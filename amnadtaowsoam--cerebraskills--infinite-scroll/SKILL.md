---
name: infinite-scroll
description: Infinite scroll is a technique for displaying large datasets by loading Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Infinite Scroll

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Infinite scroll is a technique for displaying large datasets by loading additional content as the user scrolls to a predefined threshold, instead of loading all data at once. This skill covers Intersection Observer API for detecting viewport entry, virtual scrolling to render only visible items for performance, infinite queries with React Query for cursor-based pagination, scroll position restoration, and performance optimization patterns for large datasets.

## Why This Matters
- **Reduces Initial Load Time**: Loads only necessary data initially, improving time-to-first-content
- **Increases User Engagement**: Continuous scrolling increases engagement time and content discovery
- **Reduces Bandwidth Usage**: Loads only visible items, reducing unnecessary bandwidth consumption
- **Improves Conversion Rate**: Continuous content display can increase conversion rates by 15-25%
- **Enhances Mobile Performance**: Virtual scrolling improves performance on mobile devices with limited hardware
- **Improves User Retention**: Good scrolling experience creates satisfaction in usage, making users return to use application again

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
  - Dataset or list items
  - Performance requirements
  - User interaction requirements
  - Accessibility guidelines
* **Entry Conditions**:
  - React 16+ is available
  - Intersection Observer API is supported
  - React Query is available
  - Design system is established
  - Performance baseline is measured
* **Outputs**:
  - Infinite scroll components
  - Virtual scrolling implementation
  - Performance-optimized code
  - Accessible implementations
* **Artifacts Required (Deliverables)**:
  - Reusable infinite scroll components
  - Virtual scrolling utilities
  - Performance monitoring setup
  - Accessibility testing results
* **Acceptance Evidence**:
  - Scrolling is smooth at 60 FPS
  - CLS score < 0.1
  - Accessibility passes WCAG 2.1
  - Bundle size impact is minimal
* **Success Criteria**:
  - Scrolling is smooth and performant
  - Accessibility requirements are met
  - Performance targets are achieved
  - User experience is good

## Skill Composition
* **Depends on**: [nextjs-patterns](../nextjs-patterns/SKILL.md), [react-best-practices](../react-best-practices/SKILL.md)
* **Compatible with**: [animation](../animation/SKILL.md), [error-boundaries-react](../error-boundaries-react/SKILL.md)
* **Conflicts with**: None
* **Related Skills**: [state-management](../state-management/SKILL.md), [tailwind-patterns](../tailwind-patterns/SKILL.md)

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
