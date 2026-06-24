---
name: api-versioning-strategies
description: API versioning protects clients from breaking changes while allowing Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Api Versioning Strategies

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
API versioning protects clients from breaking changes while allowing servers to evolve. This guide covers strategies, lifecycle management, and migration practices for maintaining backward compatibility across API evolution.

## Why This Matters
API versioning is critical for:

- **Client Stability**: Prevents breaking changes from disrupting existing integrations
- **Trust Building**: Establishes predictable evolution patterns for API consumers
- **Migration Safety**: Enables controlled, gradual transitions to new API versions
- **Business Continuity**: Allows both old and new clients to coexist during transitions
- **Compliance**: Meets enterprise requirements for backward compatibility guarantees

Poor versioning leads to:
- Client breakage and downtime
- Loss of developer trust
- Increased support burden
- Difficulty maintaining multiple client versions
- Forced emergency deployments

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
- Clients have the ability to update their code within the deprecation window
- You have visibility into client usage patterns
- You can maintain multiple versions of API handlers
- Documentation is kept in sync with each API version
- Version routing can be implemented at the infrastructure level

## Compatibility
- Works with all REST frameworks
- Compatible with GraphQL (uses different versioning approach)
- Supports all HTTP clients (curl, Axios, fetch, etc.)
- Integrates with API gateways (AWS API Gateway, Kong, etc.)
- Compatible with documentation tools (Swagger, Redoc, etc.)

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


## Agent Directives
**When Creating a New API Version:**
1. Analyze the proposed changes to determine if a new version is needed
2. Choose appropriate versioning strategy (URI, header, etc.)
3. Create version-specific handlers and routing
4. Add deprecation headers to the old version
5. Write comprehensive migration guide
6. Update documentation for both versions
7. Implement monitoring for version usage

**When Deprecating a Version:**
1. Check usage statistics to ensure low adoption
2. Set appropriate sunset date (typically 6-12 months)
3. Add deprecation headers to all endpoints
4. Notify all affected clients
5. Publish migration guide
6. Monitor for continued usage after deprecation

**When Handling Version-Specific Errors:**
1. Return appropriate HTTP status codes
2. Include version information in error responses
3. Provide migration guidance in error messages
4. Log version-specific errors separately

## Definition of Done (DoD) Checklist

- [ ] Tests passed + coverage met
- [ ] Lint/Typecheck passed
- [ ] Logging/Metrics/Trace implemented
- [ ] Security checks passed
- [ ] Documentation/Changelog updated
- [ ] Accessibility/Performance requirements met (if frontend)


## Anti-patterns
**Silent Breaking Changes**
- Making breaking changes without version bump
- Changing behavior without documentation
- Removing fields without deprecation

**Too Many Parallel Versions**
- Maintaining more than 3 active versions
- Creating new versions for minor changes
- Failing to sunset old versions

**Unclear Version Headers**
- Using non-standard version headers
- Inconsistent version naming schemes
- Missing deprecation information

**Short Deprecation Windows**
- Less than 6 months notice for breaking changes
- No grace period for migration
- Immediate sunset without communication

**Inconsistent Versioning**
- Mixing versioning strategies
- Different patterns for different endpoints
- No clear versioning policy

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
