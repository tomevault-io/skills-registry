---
name: claude-security-review
description: Security-focused review for frontend/Web3 code. Use for XSS, wallet security, CSP, and dependency checks. Use when this capability is needed.
metadata:
  author: hyperlane-xyz
---

# Security Review Skill

Use this skill for security-focused code review of frontend Web3 code.

## When to Use

- Reviewing wallet integration code
- Checking for XSS vulnerabilities
- CSP header changes
- Dependency updates

## Instructions

Read and apply the security guidelines from `.github/prompts/security-scan.md` to review the code changes.

Report findings with severity ratings (Critical/High/Medium/Low/Informational) and suggested fixes.

### For PR Reviews

When reviewing a PR, deliver feedback using `/inline-pr-comments` to post inline comments on specific lines.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hyperlane-xyz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
