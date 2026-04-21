---
name: review-project
description: Analyze project health including structural issues, security vulnerabilities, performance concerns, and code stability metrics. Use when auditing codebases, reviewing project quality, checking for security issues, identifying architectural problems, measuring code health, or generating quality reports. Use when this capability is needed.
metadata:
  author: runxgalee
---

## Purpose

Provide comprehensive project health analysis covering structural integrity, security posture, performance characteristics, and overall code stability. This skill leverages the codebase-analyzer agent along with specialized sub-agents to deliver a thorough quality assessment.

## When to Use

Use this skill when you need to:

- **Audit project health** - Get a complete quality assessment of the codebase
- **Identify structural issues** - Find architectural problems and anti-patterns
- **Security review** - Detect potential vulnerabilities and security concerns
- **Performance analysis** - Identify performance bottlenecks and inefficiencies
- **Code stability check** - Assess test coverage, error handling, and reliability
- **Pre-release review** - Validate codebase before major releases
- **Technical debt assessment** - Quantify and prioritize technical debt

## Analysis Categories

### 1. Structural Analysis

Evaluate the overall architecture and organization:

#### Directory Structure
- **Consistency** - Naming conventions, folder organization
- **Separation of Concerns** - Clear boundaries between layers
- **Module Boundaries** - Proper encapsulation and dependencies
- **Dead Code** - Unused files, orphaned modules

#### Architecture Patterns
- **Pattern Consistency** - Adherence to chosen patterns (MVC, Clean Architecture, etc.)
- **Dependency Direction** - Proper dependency flow (inner layers don't depend on outer)
- **Circular Dependencies** - Identify and report cycles
- **Coupling Analysis** - Measure inter-module dependencies

#### Code Organization
- **File Size** - Identify overly large files (>500 lines)
- **Function Complexity** - Cyclomatic complexity analysis
- **Naming Conventions** - Consistency in naming
- **Code Duplication** - Detect copy-paste patterns

### 2. Security Analysis

Identify potential security vulnerabilities:

#### OWASP Top 10 Checks
- **Injection Flaws** - SQL, NoSQL, OS command injection patterns
- **Broken Authentication** - Weak auth patterns, hardcoded credentials
- **Sensitive Data Exposure** - Unencrypted secrets, logging PII
- **XML External Entities (XXE)** - Unsafe XML parsing
- **Broken Access Control** - Missing authorization checks
- **Security Misconfiguration** - Debug modes, default configs
- **XSS** - Unsanitized user input in output
- **Insecure Deserialization** - Unsafe object serialization
- **Vulnerable Dependencies** - Known CVEs in dependencies
- **Insufficient Logging** - Missing audit trails

#### Code-Level Security
- **Secret Management** - Hardcoded API keys, passwords
- **Input Validation** - Missing or weak validation
- **Output Encoding** - Proper escaping for context
- **Error Handling** - Information leakage in errors
- **Cryptography** - Weak algorithms, improper usage

### 3. Performance Analysis

Identify performance concerns:

#### Code Efficiency
- **Algorithm Complexity** - O(n²) or worse patterns
- **Memory Leaks** - Unclosed resources, unbounded caches
- **N+1 Queries** - Database query patterns
- **Unnecessary Operations** - Redundant computations

#### Resource Management
- **Connection Pooling** - Database, HTTP connections
- **Caching Strategy** - Missing or ineffective caching
- **Async Operations** - Blocking calls in async contexts
- **Bundle Size** - Large dependencies, unused imports (frontend)

#### Scalability Concerns
- **State Management** - Stateful components that prevent scaling
- **Race Conditions** - Concurrent access patterns
- **Resource Limits** - Hardcoded limits, unbounded growth

### 4. Code Stability

Assess reliability and maintainability:

#### Test Coverage
- **Unit Tests** - Coverage percentage, critical path coverage
- **Integration Tests** - API and service integration testing
- **E2E Tests** - User journey coverage
- **Test Quality** - Assertions, edge cases, mocking patterns

#### Error Handling
- **Exception Coverage** - Unhandled exceptions
- **Graceful Degradation** - Fallback mechanisms
- **Recovery Patterns** - Retry logic, circuit breakers
- **Error Reporting** - Proper error classification and logging

#### Code Quality
- **Type Safety** - TypeScript strict mode, type coverage
- **Null Safety** - Null/undefined handling
- **Documentation** - Critical function documentation
- **TODO/FIXME** - Outstanding technical debt markers

## Health Score Calculation

Generate a health score (0-100) based on weighted categories:

| Category | Weight | Metrics |
|----------|--------|---------|
| Structure | 25% | Architecture consistency, code organization, complexity |
| Security | 30% | Vulnerability count, secret exposure, dependency health |
| Performance | 20% | Efficiency patterns, resource management |
| Stability | 25% | Test coverage, error handling, type safety |

### Scoring Rubric

**Structure (25 points)**
- 25: Clean architecture, consistent patterns, low complexity
- 20: Minor inconsistencies, acceptable complexity
- 15: Some architectural issues, moderate complexity
- 10: Significant structural problems
- 5: Major architectural issues, high complexity

**Security (30 points)**
- 30: No vulnerabilities, proper secret management, secure patterns
- 24: Minor security concerns, no critical issues
- 18: Some vulnerabilities, needs attention
- 12: Multiple security issues
- 6: Critical security vulnerabilities

**Performance (20 points)**
- 20: Efficient code, proper resource management
- 16: Minor inefficiencies
- 12: Some performance concerns
- 8: Significant performance issues
- 4: Major performance problems

**Stability (25 points)**
- 25: High test coverage (>80%), comprehensive error handling
- 20: Good coverage (60-80%), adequate error handling
- 15: Moderate coverage (40-60%), basic error handling
- 10: Low coverage (20-40%), minimal error handling
- 5: Very low coverage (<20%), poor error handling

## Output Format

Generate a comprehensive health report:

```markdown
# Project Health Report

## Summary
- **Overall Health Score**: XX/100
- **Risk Level**: Low/Medium/High/Critical
- **Assessment Date**: YYYY-MM-DD

## Category Scores
| Category | Score | Status |
|----------|-------|--------|
| Structure | XX/25 | 🟢/🟡/🔴 |
| Security | XX/30 | 🟢/🟡/🔴 |
| Performance | XX/20 | 🟢/🟡/🔴 |
| Stability | XX/25 | 🟢/🟡/🔴 |

## Critical Issues (Immediate Action Required)
1. [Issue description with file:line reference]
2. ...

## High Priority Issues
1. [Issue description]
2. ...

## Medium Priority Issues
1. [Issue description]
2. ...

## Recommendations
1. [Actionable recommendation]
2. ...

## Detailed Findings

### Structure Analysis
[Detailed findings]

### Security Analysis
[Detailed findings]

### Performance Analysis
[Detailed findings]

### Stability Analysis
[Detailed findings]
```

## Integration with CI/CD

This skill can be used to:
- Generate reports for code review
- Block PRs with critical issues
- Track health metrics over time
- Set quality gates for releases

## Related Skills

- `analyze-codebase` - Deep architectural analysis
- `security-audit` - Detailed security review
- `performance-review` - Performance profiling
- `test-coverage` - Test coverage analysis

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/runxgalee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
