---
name: retry-timeout-strategies
description: In distributed systems, transient failures are inevitable. Proper retry, Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Retry Timeout Strategies

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
In distributed systems, transient failures are inevitable. Proper retry, timeout, and backoff strategies are essential for building resilient applications that gracefully handle temporary failures without overwhelming downstream services.

**Core Principle**: "Fail fast, retry smart, back off gracefully, and never give up."

## Why This Matters
- **Reliability**: Handles transient failures gracefully
- **User Experience**: Reduces perceived outages from temporary issues
- **System Health**: Prevents cascading failures from retry storms
- **Cost Efficiency**: Optimizes resource usage with appropriate timeouts
- **Observability**: Provides metrics on retry behavior and failure rates

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
  - API endpoint definitions and operation types
  - External service dependencies
  - Latency metrics and SLA requirements
* **Entry Conditions**:
  - Timeout values are configured for all operations
  - Retry logic is implemented for transient failures
  - Circuit breakers are configured for external dependencies
* **Outputs**:
  - Retry, timeout, and backoff implementations
  - Circuit breaker configurations
  - Monitoring and metrics for retry behavior
* **Artifacts Required (Deliverables)**:
  - Timeout configuration documentation
  - Retry strategy implementation
  - Circuit breaker setup
  - Monitoring dashboards for retry rates
* **Acceptance Evidence**:
  - Load test showing retry behavior under failure
  - Circuit breaker state transition logs
  - Timeout effectiveness metrics
* **Success Criteria**:
  - Transient failures are handled gracefully
  - Circuit breaker prevents cascading failures
  - Retry storms are prevented with backoff and jitter
  - Timeout values are appropriate for SLA

## Skill Composition
* **Depends on**: Idempotency, Monitoring & Observability
* **Compatible with**: Circuit Breaker, Graceful Degradation
* **Conflicts with**: Systems that cannot handle retries safely
* **Related Skills**: 
  - [40-system-resilience/idempotency-and-dedup](40-system-resilience/idempotency-and-dedup/SKILL.md) - Idempotency for safe retries
  - [40-system-resilience/graceful-degradation](40-system-resilience/graceful-degradation/SKILL.md) - Fallback strategies
  - [40-system-resilience/failure-modes](40-system-resilience/failure-modes/SKILL.md) - Understanding what to retry

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
