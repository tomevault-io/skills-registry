---
name: secure-code-reviewer
description: > Use when this capability is needed.
metadata:
  author: mittuled
---

# secure-code-reviewer

## Agent: Security Engineer

L2 security engineer (Nx) responsible for threat modelling, security requirements, architecture review, code review, penetration testing, compliance, and continuous monitoring.

Department ethos: [ideal-engineering.md](../../../../departments/engineering/ideal-engineering.md)

## Skill Description

Reviews code for security vulnerabilities including OWASP Top 10 categories, language-specific risks, and unsafe dependency usage.

## When to Use

- When a pull request modifies authentication, authorization, cryptography, or data handling logic.
- When new third-party dependencies are introduced and require supply chain risk assessment.
- When a SAST tool flags findings that require human triage to distinguish true positives from false positives.

## Workflow

1. **Change Scope Assessment**: Identify which files and functions are modified, what security-sensitive areas they touch (auth, crypto, input handling, data serialization), and the trust boundaries crossed. Deliverable: change scope summary with security-relevant annotations.
2. **OWASP Top 10 Scan**: Review the code against OWASP Top 10 categories: injection (SQL, NoSQL, OS command, LDAP), broken authentication, sensitive data exposure, XXE, broken access control, security misconfiguration, XSS, insecure deserialization, vulnerable components, and insufficient logging. Deliverable: finding list mapped to OWASP categories.
3. **Language-Specific Risk Review**: Check for language-specific vulnerabilities: prototype pollution (JavaScript), unsafe deserialization (Java/Python), buffer overflows (C/C++), SQL injection via string formatting (Python), or path traversal. Deliverable: language-specific findings with fix recommendations.
4. **Dependency Audit**: Verify new or updated dependencies against vulnerability databases (NVD, GitHub Advisory, Snyk). Check for typosquatting, unmaintained packages, and excessive transitive dependency trees. Deliverable: dependency risk assessment.
5. **Finding Report and Remediation Guidance**: Compile all findings with severity (critical, high, medium, low), code location, reproduction steps, and specific fix guidance. Deliverable: security code review report attached to the pull request.

## Anti-Patterns

- **SAST-only review**: Relying entirely on static analysis tool output without manual code review. *Why*: SAST tools produce high false-positive rates and miss logic-level vulnerabilities like broken access control or business logic bypasses.
- **Reviewing only the diff**: Ignoring the surrounding context of modified functions. *Why*: a safe-looking change can introduce a vulnerability when combined with existing unsafe patterns in the same module.
- **Approving with known medium-severity findings**: Merging code with unresolved medium-severity security findings under time pressure. *Why*: medium-severity vulnerabilities compound and become exploitable chains.

## Output

**On success**: Produces a security code review report containing findings mapped to OWASP categories, language-specific risks, dependency audit results, and remediation guidance. Delivered as comments on the pull request.

**On failure**: Report which review dimensions could not be completed (e.g., obfuscated code, unavailable dependency metadata), what was assessed, and recommended follow-up actions.

## Related Skills

- [`penetration-tester`](../penetration-tester/SKILL.md) -- Validates code-level vulnerabilities through exploitation.
- [`security-requirements-extractor`](../security-requirements-extractor/SKILL.md) -- Provides the security requirements that code must satisfy.

---
> Source: [mittuled/skill-os](https://github.com/mittuled/skill-os) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
