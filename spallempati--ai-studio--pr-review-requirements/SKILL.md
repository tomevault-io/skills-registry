---
name: pr-review-requirements
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# Peer-Review and Automation Gate Required Before Merge

## Description

This rule mandates that every pull request must be reviewed and approved by at
least one human and must pass all automated quality gates—such as lint checks
and security scans—before it is eligible for merge. This rule enforces
collaborative validation and prevents high-risk code from entering production.

## Purpose

To ensure software quality, accountability, and auditability, this rule
reinforces that no single developer can push unreviewed code to production. It
protects against missed bugs, security issues, and coding standard violations,
aligning with secure SDLC and compliance mandates.

## Scope

- All application and backend repositories
- Pull requests targeting protected branches
- Applies to developers, reviewers, and maintainers
- Enforced across GitHub, GitLab, or Bitbucket-based CI/CD pipelines

## SDLC Integration

- **Planning**: Defines required peer involvement and tooling
- **Analysis**: Drives requirement coverage validation via peer input
- **Design**: Encourages design discussions in PR comments
- **Development**: Requires formal PR for merge eligibility
- **Testing**: Automated gates validate code quality and security
- **Deployment**: Ensures traceable change history
- **Maintenance**: Prevents unapproved regressions or workarounds

## Standards

### Review and Validation

- Pull requests **MUST** receive at least one human approval before merge
- Automated linting and static analysis **MUST** report 0 critical issues before
  merge
- Security scans **MUST** pass for all changes to merge
- Code review comments **SHOULD** be resolved before approval

## Actionable Metrics

| Metric                        | Target Value | Measurement Method  | Enforcement Level |
| ----------------------------- | ------------ | ------------------- | ----------------- |
| Human review approvals per PR | ≥ 1          | GitHub API          | **MUST**          |
| Critical lint/security issues | 0            | Linter/SAST reports | **MUST**          |
| Time to first review          | ≤ 24 hrs     | PR activity log     | **SHOULD**        |

## Implementation

### Configuration Requirements

- Enable branch protection rules:
  - Require pull request reviews before merging
  - Require status checks to pass before merging
  - Enforce security scan and lint checks in CI

#### Example: Correct Implementation

```java
// PR on RefundProcessor.java receives teammate approval and passes SAST/linter
public class RefundProcessor {
  ...
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
