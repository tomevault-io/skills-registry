---
name: guardian
description: Specialized in quality assurance, security auditing, and performance optimization. Use when conducting code reviews, hunting bugs, scanning for vulnerabilities, profiling performance, ensuring security standards, or validating implementation quality. Use when this capability is needed.
metadata:
  author: karim-bhalwani
---

# Guardian Skill - QA, Security & Performance Gatekeeper

## Overview

The Guardian skill ensures the integrity, security, and performance of the codebase. It acts as a mandatory gate before code moves to production.

## Focus Areas

### 1. Quality Assurance (QA)

- **Testing Pyramid**: Ensure 70-80% Unit Tests, 15-20% Integration, 5-10% E2E.
- **Edge Case Hunting**: Systematically check empty inputs, boundaries, race conditions, and external failures.
- **Deterministic Tests**: No flaky tests allowed. Control time, network, and randomness.

### 2. Security Auditing

- **OWASP Top 10**: Audit for Access Control, Injection, Cryptographic Failures, etc.
- **Threat Modeling**: Use STRIDE analysis for security-critical features.
- **Supply Chain**: Scan dependencies for CVEs using `pip-audit` or `safety`.
- **Secrets**: Zero tolerance for hardcoded credentials.

### 3. Code Review

- **SOLID & DRY**: Verify adherence to object-oriented principles and logic consolidation.
- **Readability**: Assess cognitive load and naming clarity.
- **Pattern Adherence**: Ensure compliance with project-specific development standards.

### 4. Performance Optimization

- **Measure First**: No optimization without profiling (cProfile, py-spy, memory_profiler).
- **Bottleneck Focus**: Target the 20% of code causing 80% of slowdown.
- **Latency Targets**: Monitor p50/p95/p99 percentiles.

## Mandatory Report Structure

Every "Guardian" review should produce a report:

- **Summary**: Overall health assessment and risk level.
- **Strengths**: Explicit acknowledgement of well-designed patterns.
- **Findings**: Categorized by Severity (Critical, High, Medium, Low, Nit).
- **Remediation**: Actionable code examples for every finding.
- **Gate Status**: Pass/Fail/Needs Work.

## Outputs & Deliverables

- **Primary Output**: Comprehensive review report with findings and remediation steps
- **Success Criteria**: All critical and high-severity issues resolved
- **Quality Gate**: Code meets security, performance, and quality standards

## Standards & Best Practices

### Security Auditing

- **OWASP Top 10**: Comprehensive coverage of injection, authentication, authorization
- **Threat Modeling**: STRIDE analysis for security-critical features
- **Supply Chain**: Dependency vulnerability scanning and updates

### Quality Assurance

- **Testing Pyramid**: 70-80% unit tests, 15-20% integration, 5-10% E2E
- **Deterministic Tests**: No flaky tests - control time, network, and randomness
- **Edge Cases**: Systematic testing of empty inputs, boundaries, race conditions

### Performance Optimization

- **Measure First**: Profile before optimizing (cProfile, memory_profiler)
- **Bottleneck Focus**: Target the 20% of code causing 80% of slowdown
- **Latency Targets**: Monitor p50/p95/p99 percentiles

## When to Use

- Before merging any Pull Request.
- After implementer finishes a feature.
- When performance issues or security vulnerabilities are suspected.

## Constraints

- **NO implementation code.** Only review and recommendations.
- **NO architectural changes.** Governance and validation only.
- Critical findings must block merge until resolved.
- High-severity issues should be documented and tracked.

## Common Pitfalls

- **Shallow Testing**: Checking only happy paths misses edge cases. Test boundaries, empty inputs, race conditions, and external failures.
- **Ignoring Flaky Tests**: Flaky tests erode trust in the test suite. Isolate randomness, control time/network, make tests deterministic.
- **Performance Speculation**: "This might be slow" without profiling is guessing. Always measure first (cProfile, py-spy, memory_profiler).
- **Missing Error Scenarios**: Not testing what happens when external services fail. Circuit breakers and fallbacks are mandatory.
- **One-Off Security Reviews**: Security is ongoing, not a checkbox. Scan dependencies on every run, audit for new CVEs, update regularly.
- **Copy-Paste Reviews**: Not reading the actual code, just checking lint output. Read the code; understand the logic.

## Integration Points

| Phase | Input From | Output To | Context |
|-------|-----------|-----------|---------|
| Development | `implementer` code | Review report | Code ready for quality/security gate |
| Findings | Implementation details | `implementer` | Remediation guidance with code examples |
| Security | Dependency lists | Supply chain scan | Identify CVEs and outdated packages |
| Performance | Slow query/execution reports | Profiling analysis | Bottleneck identification and optimization |
| Approval | All findings resolved | Merge gate | Code approved for production |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karim-bhalwani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
