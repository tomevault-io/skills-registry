---
name: api-design
description: RESTful API design is an architectural style and set of constraints for Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Api Design

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
RESTful API design is an architectural style and set of constraints for building web services that emphasize consistency, scalability, and developer experience. This skill provides comprehensive guidance on REST principles, HTTP method semantics, URL structure, status codes, authentication, pagination, and documentation to create APIs that are intuitive, maintainable, and evolve gracefully over time.

## Why This Matters
- **Enables Ecosystem Growth**: Consistent, well-documented APIs allow third-party developers to integrate easily with your platform
- **Reduces Integration Friction**: Clear patterns and standard response formats reduce developer confusion and integration errors
- **Improves Developer Experience**: Predictable URLs, clear error messages, and intuitive design make APIs pleasant to work with
- **Supports Scalability**: Proper resource modeling and architectural patterns enable systems to grow without major redesigns
- **Ensures Client Compatibility**: Versioning strategies and backward compatibility policies prevent breaking existing consumers
- **Reduces Support Costs**: Self-documenting APIs and standardizing responses reduce documentation burden and support ticket volume

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
  - Business requirements and use cases
  - Resource models and relationships
  - Security and compliance requirements
  - Performance and scalability targets
* **Entry Conditions**:
  - API requirements are defined
  - Resource models are available
  - Security requirements are understood
* **Outputs**:
  - API specification (OpenAPI/Swagger)
  - Endpoint documentation
  - Request/response schemas
  - Error handling specification
  - Authentication and authorization design
* **Artifacts Required (Deliverables)**:
  - OpenAPI specification file
  - API documentation
  - Postman collection or similar
  - Integration examples
* **Acceptance Evidence**:
  - API specification is complete and validated
  - All endpoints are documented
  - Authentication and authorization are implemented
  - Rate limiting is configured
* **Success Criteria**:
  - API follows REST principles
  - Documentation is complete and accurate
  - All endpoints are tested
  - Security measures are in place

## Skill Composition
* **Depends on**: [error-handling](../03-backend-api/error-handling/SKILL.md), [validation](../03-backend-api/validation/SKILL.md)
* **Compatible with**: [authentication-authorization](../10-authentication-authorization/), [graphql-best-practices](../03-backend-api/graphql-best-practices/SKILL.md)
* **Conflicts with**: None
* **Related Skills**: [fastapi-patterns](../03-backend-api/fastapi-patterns/SKILL.md), [express-rest](../03-backend-api/express-rest/SKILL.md), [nestjs-patterns](../03-backend-api/nestjs-patterns/SKILL.md)

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
