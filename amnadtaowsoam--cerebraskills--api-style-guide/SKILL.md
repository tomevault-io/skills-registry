---
name: api-style-guide
description: Organization-wide API design standards (REST/GraphQL): URL patterns, Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Api Style Guide

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Organization-wide API design standards (REST/GraphQL): URL patterns, naming, versioning, pagination, and filtering that make every API work consistently.

## Why This Matters
- **Consistency**: Clients know how to use API without reading docs every time
- **Predictability**: Can guess what endpoint looks like
- **Tooling**: Auto-generate clients
- **Maintainability**: Easy to change maintainers

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
* **Depends on**: [api-design](../../01-foundations/api-design/SKILL.md), [error-handling](../../03-backend-api/error-handling/SKILL.md)
* **Compatible with**: [validation](../../03-backend-api/validation/SKILL.md), [contract-test-gates](../68-quality-gates-ci-policies/contract-test-gates/SKILL.md)
* **Conflicts with**: None
* **Related Skills**: [openapi-spec](../../03-backend-api/openapi-spec/SKILL.md), [graphql-best-practices](../../03-backend-api/graphql-best-practices/SKILL.md)

## Quick Start
#

## Assumptions
- Organization uses REST and/or GraphQL APIs
- Teams need consistent API patterns
- API clients include web, mobile, and third-party integrations
- API documentation is required
- Versioning strategy needed for long-term API evolution

## Compatibility
- **REST**: HTTP/1.1, HTTP/2
- **GraphQL**: GraphQL spec latest
- **OpenAPI**: 3.0.3+
- **JSON**: JSON:API or custom envelope
- **Authentication**: OAuth2, JWT, mTLS

## Test Scenario Matrix
| Scenario | Input | Expected Output | Verification |
|----------|-------|-----------------|--------------|
| List resources | GET /api/v1/users | Paginated list of users | Response structure |
| Create resource | POST /api/v1/users | Created user with 201 status | Location header |
| Update resource | PATCH /api/v1/users/{id} | Updated user with 200 status | Response body |
| Delete resource | DELETE /api/v1/users/{id} | Deleted with 204 status | No response body |
| Filter resources | GET /api/v1/users?status=active | Filtered list | Query parameters |
| Invalid request | POST with invalid data | 400 error response | Error shape |

## Technical Guardrails
#

## Agent Directives & Error Recovery
*(ข้อกำหนดสำหรับ AI Agent ในการคิดและแก้ปัญหาเมื่อเกิดข้อผิดพลาด)*

- **Thinking Process**: Analyze root cause before fixing. Do not brute-force.
- **Fallback Strategy**: Stop after 3 failed test attempts. Output root cause and ask for human intervention/clarification.
- **Self-Review**: Check against Guardrails & Anti-patterns before finalizing.
- **Output Constraints**: Output ONLY the modified code block. Do not explain unless asked.


## Definition of Done
An API is complete when:

- [ ] URLs follow naming conventions (kebab-case, plural nouns)
- [ ] HTTP methods used correctly
- [ ] Versioning strategy documented and implemented
- [ ] Request/response formats follow standards
- [ ] Pagination implemented consistently
- [ ] Error responses follow error shape taxonomy
- [ ] Authentication and authorization implemented
- [ ] OpenAPI/GraphQL specification maintained
- [ ] API documentation complete
- [ ] Contract tests written and passing

## Anti-patterns
1. **Verbs in URLs**: `/api/getUsers` instead of `/api/users`
2. **Inconsistent casing**: `userId` vs `user_id`
3. **No versioning**: Breaking changes break clients
4. **Different pagination**: Cursor here, offset there
5. **No standard envelope**: Different response structures
6. **Inconsistent error shapes**: Different error formats
7. **Missing authentication**: Unprotected endpoints
8. **No rate limiting**: API abuse possible

## Reference Links
- [Google API Design Guide](https://cloud.google.com/apis/design)
- [Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines)
- [JSON:API Specification](https://jsonapi.org/)
- [OpenAPI Specification](https://swagger.io/specification/)
- [GraphQL Best Practices](https://graphql.org/learn/best-practices/)

## Versioning & Changelog

* **Version**: 1.0.0
* **Changelog**:
  - 2026-02-22: Initial version with complete template structure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
