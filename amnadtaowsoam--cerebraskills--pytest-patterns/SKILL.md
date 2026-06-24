---
name: pytest-patterns
description: Pytest is a powerful testing framework for Python that makes it easy Use when this capability is needed.
metadata:
  author: AmnadTaowsoam
---

# Pytest Patterns

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Pytest is a powerful testing framework for Python that makes it easy to write simple yet scalable test cases. This skill covers pytest basics, test structure, fixtures, parametrization, markers, mocking, async tests, coverage, FastAPI testing, and database testing.

## Why This Matters
Pytest provides:
- Simple and intuitive test syntax
- Powerful fixture system for test setup
- Built-in assertion introspection
- Extensive plugin ecosystem
- Parallel test execution
- Excellent integration with CI/CD

## Core Concepts
1. **Test Discovery**: Automatically finds test files matching patterns
2. **Fixtures**: Reusable test setup and teardown
3. **Parametrization**: Run tests with multiple inputs
4. **Markers**: Categorize and select tests
5. **Mocking**: Isolate code under test
6. **Async Support**: Test async code with pytest-asyncio
7. **Coverage**: Measure test coverage with pytest-cov

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
- Python 3.8+ is installed
- Project uses pytest as the test framework
- Source code follows Python best practices

## Compatibility
- **Python**: 3.8+
- **pytest**: 7.0+
- **pytest-asyncio**: 0.21+
- **pytest-mock**: 3.10+

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
1. Always run tests before committing
2. Use fixtures to reduce duplication
3. Mock external dependencies
4. Use parametrization for multiple inputs
5. Keep tests independent
6. Use markers to categorize tests

## Definition of Done (DoD) Checklist

- [ ] Tests passed + coverage met
- [ ] Lint/Typecheck passed
- [ ] Logging/Metrics/Trace implemented
- [ ] Security checks passed
- [ ] Documentation/Changelog updated
- [ ] Accessibility/Performance requirements met (if frontend)


## Anti-patterns
1. **Testing Implementation Details**
   ```python
   # Bad
   def test_internal_method():
       user = User("John", "john@example.com")
       assert user._internal_method() == "result"

   # Good
   def test_public_behavior():
       user = User("John", "john@example.com")
       assert user.full_name == "John (john@example.com)"
   ```

2. **Multiple Assertions Per Test**
   ```python
   # Bad
   def test_user():
       user = User("John", "john@example.com")
       assert user.name == "John"
       assert user.email == "john@example.com"
       assert user.id is None

   # Good
   def test_user_name():
       user = User("John", "john@example.com")
       assert user.name == "John"

   def test_user_email():
       user = User("John", "john@example.com")
       assert user.email == "john@example.com"
   ```

3. **Hardcoded Test Data**
   ```python
   # Bad
   def test_addition():
       assert 1 + 1 == 2
       assert 2 + 2 == 4
       assert 3 + 3 == 6

   # Good
   @pytest.mark.parametrize("a,b,expected", [
       (1, 1, 2),
       (2, 2, 4),
       (3, 3, 6),
   ])
   def test_addition(a, b, expected):
       assert a + b == expected
   ```

## Reference Links & Examples

* Internal documentation and examples
* Official documentation and best practices
* Community resources and discussions


## Versioning & Changelog

* **Version**: 1.0.0
* **Changelog**:
  - 2026-02-22: Initial version with complete template structure

---
> Source: [AmnadTaowsoam/CerebraSkills](https://github.com/AmnadTaowsoam/CerebraSkills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
