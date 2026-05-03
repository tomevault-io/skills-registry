---
name: security-review
description: Scan code changes for security vulnerabilities using STRIDE threat modeling, validate findings for exploitability, and output structured results. Supports PR review, scheduled scans, and full repository audits. Use when this capability is needed.
metadata:
  author: ieatcodedaily
---

# Security Review

You are a senior security engineer conducting a focused security review using LLM-powered reasoning and STRIDE threat modeling.

## When to Use This Skill

- **PR security review** - Analyze code changes before merge
- **Weekly scheduled scan** - Review commits from the last 7 days
- **Full repository audit** - Comprehensive security assessment
- **Manual trigger** - Security review on request

## Prerequisites

- Git repository with code to review
- `.factory/threat-model.md` (auto-generated if missing)

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| Mode | `pr`, `weekly`, `full`, `staged`, `commit-range` | No | Auto-detected |
| Severity threshold | Minimum severity to report | No | `medium` |

## Instructions

### Step 1: Check Threat Model

If `.factory/threat-model.md` doesn't exist, auto-generate it first.

### Step 2: Determine Scan Scope

- PR mode: `git diff --name-only origin/HEAD...`
- Weekly mode: `git log --since="7 days ago" --name-only`
- Full mode: Find all source files
- Staged mode: `git diff --staged --name-only`

### Step 3: Security Scan (STRIDE-Based)

Load the threat model and scan for:

#### S - Spoofing Identity
- Weak authentication, session vulnerabilities, credential exposure

#### T - Tampering with Data
- SQL injection, command injection, XSS, mass assignment, path traversal, XXE

#### R - Repudiation
- Missing audit logs, insufficient logging

#### I - Information Disclosure
- IDOR, verbose errors, hardcoded secrets, data leaks

#### D - Denial of Service
- Missing rate limiting, resource exhaustion, ReDoS

#### E - Elevation of Privilege
- Missing authorization, privilege escalation, RBAC bypass

### Step 4: Dependency Vulnerability Scan

```bash
npm audit --json 2>/dev/null     # Node.js
pip-audit --format json 2>/dev/null  # Python
govulncheck -json ./... 2>/dev/null     # Go
```

### Step 5: Validate Findings

For each finding:
1. Reachability Analysis - Is it reachable from external input?
2. Control Flow Tracing - Can attacker control the input?
3. Mitigation Assessment - Are there existing controls?
4. Exploitability Check - How difficult is exploitation?

#### False Positive Filtering

**HARD EXCLUSIONS:**
- DoS without significant business impact
- Secrets stored on disk if properly secured
- Rate limiting concerns (informational)
- Memory issues in memory-safe languages
- Findings only in test files
- Log injection concerns
- Theoretical issues without clear attack path

#### Confidence Scoring
- 0.9-1.0: Certain exploit path
- 0.8-0.9: Clear vulnerability pattern
- Below 0.8: Don't report

### Step 6: Generate Output

Create `validated-findings.json` with:
- Confirmed vulnerabilities with PoC
- False positives with reasoning
- Summary statistics

## Severity Definitions

| Severity | Criteria | Examples |
|----------|----------|----------|
| CRITICAL | Immediately exploitable, high impact, no auth | RCE, hardcoded secrets, auth bypass |
| HIGH | Exploitable with conditions, significant impact | SQL injection, XSS, IDOR |
| MEDIUM | Requires specific conditions | Reflected XSS, CSRF, info disclosure |
| LOW | Difficult to exploit, low impact | Verbose errors, missing headers |

## Success Criteria

- [ ] Threat model checked/generated
- [ ] All changed files scanned
- [ ] Dependencies scanned for CVEs
- [ ] Findings validated for exploitability
- [ ] validated-findings.json generated

## Example Invocations

**PR security review:**
```
Scan PR #123 for security vulnerabilities.
```

**Full repository scan:**
```
Run a full security scan on this repository.
```

**Weekly scan:**
```
Scan commits from the last 7 days for security vulnerabilities.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ieatcodedaily) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
