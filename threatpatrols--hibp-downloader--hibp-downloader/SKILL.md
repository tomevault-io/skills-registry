---
name: vulnerability-scanner
description: Advanced vulnerability analysis principles. OWASP 2025, Supply Chain Security, attack surface mapping, risk prioritization. Use when this capability is needed.
metadata:
  author: threatpatrols
---

# Vulnerability Scanner

> Think like an attacker, defend like an expert. 2025 threat landscape awareness.

## 🔧 Runtime Scripts

**Execute for automated validation:**

| Script | Purpose | Usage |
|--------|---------|-------|
| `scripts/security_scan.py` | Validate security principles applied | `python scripts/security_scan.py <project_path>` |

## 📋 Reference Files

| File | Purpose |
|------|---------|
| [checklists.md](checklists.md) | OWASP Top 10, Auth, API, Data protection checklists |

---

## 1. Security Expert Mindset

### Core Principles

| Principle | Application |
|-----------|-------------|
| **Assume Breach** | Design as if attacker already inside |
| **Zero Trust** | Never trust, always verify |
| **Defense in Depth** | Multiple layers, no single point |
| **Least Privilege** | Minimum required access only |
| **Fail Secure** | On error, deny access |

### Threat Modeling Questions

Before scanning, ask:
1. What are we protecting? (Assets)
2. Who would attack? (Threat actors)
3. How would they attack? (Attack vectors)
4. What's the impact? (Business risk)

---

## 2. OWASP Top 10:2025/2026

### Risk Categories

| Rank | Category | Think About |
|------|----------|-------------|
| **A01** | Broken Access Control | Who can access what? IDOR, SSRF |
| **A02** | Security Misconfiguration | Defaults, headers, exposed services |
| **A03** | Software Supply Chain 🆕 | Dependencies, CI/CD, build integrity |
| **A04** | Cryptographic Failures | Weak crypto, exposed secrets |
| **A05** | Injection | User input → system commands |
| **A06** | Insecure Design | Flawed architecture |
| **A07** | Authentication Failures | Session, credential management |
| **A08** | Integrity Failures | Unsigned updates, tampered data |
| **A09** | Logging & Alerting | Blind spots, no monitoring |
| **A10** | Exceptional Conditions 🆕 | Error handling, fail-open states |

### 2025/2026 Key Changes

```
2021 → 2025/2026 Shifts:
├── SSRF merged into A01 (Access Control)
├── A02 elevated (Cloud/Container configs)
├── A03 NEW: Supply Chain (major focus)
├── A10 NEW: Exceptional Conditions
└── Agentic Shift: OWASP Agentic Top 10 (ASI04) addresses prompt & tool supply chains
```

---

## 3. Supply Chain Security (A03)

### Attack Surface

| Vector | Risk | Question to Ask |
|--------|------|-----------------|
| **Dependencies** | Malicious packages | Do we audit new deps? |
| **Lock files** | Integrity attacks | Are they committed? |
| **Build pipeline** | CI/CD compromise | Who can modify? |
| **Registry** | Typosquatting / Poisoning | Verified sources? |
| **Cooldown Period** | Zero-day package uploads | Do we use `exclude-newer`? |

### Defense Principles

- Verify package integrity (checksums/hashes in `uv.lock`)
- Enforce dependency cooldowns (use `exclude-newer` in `pyproject.toml`) to delay using brand new package releases until community vetted
- Pin exact versions in lock files, audit updates with automated tools (e.g. `pip-audit`)
- Sign and verify artifacts

---

## 4. Agentic Supply Chain (ASI04:2026)

### Attack Surface

| Vector | Risk | Question to Ask |
|--------|------|-----------------|
| **Prompt Templates** | Dynamic prompt injection | Are templates sanitized? |
| **Tool Definitions** | Executing malicious tools | Are tools pinned and verified? |
| **MCP Servers** | Rogue servers stealing data | Is MCP transport secure/auth'd? |
| **API Boundaries** | Data leakage to external LLMs | Are payloads size-bounded? |

### Defense Principles

- Enforce zero-trust on dynamic inputs injected into prompts
- Validate and authenticate all Model Context Protocol (MCP) server endpoints
- Prevent dynamic import or execution of tool code supplied by untrusted APIs
- Strictly separate system prompt instructions from user-provided content

---


## 5. Attack Surface Mapping

### What to Map

| Category | Elements |
|----------|----------|
| **Entry Points** | APIs, forms, file uploads |
| **Data Flows** | Input → Process → Output |
| **Trust Boundaries** | Where auth/authz checked |
| **Assets** | Secrets, PII, business data |

### Prioritization Matrix

```
Risk = Likelihood × Impact

High Impact + High Likelihood → CRITICAL
High Impact + Low Likelihood  → HIGH
Low Impact + High Likelihood  → MEDIUM
Low Impact + Low Likelihood   → LOW
```

---

## 6. Risk Prioritization

### CVSS + Context

| Factor | Weight | Question |
|--------|--------|----------|
| **CVSS Score** | Base severity | How severe is the vuln? |
| **EPSS Score** | Exploit likelihood | Is it being exploited? |
| **Asset Value** | Business context | What's at risk? |
| **Exposure** | Attack surface | Internet-facing? |

### Prioritization Decision Tree

```
Is it actively exploited (EPSS >0.5)?
├── YES → CRITICAL: Immediate action
└── NO → Check CVSS
         ├── CVSS ≥9.0 → HIGH
         ├── CVSS 7.0-8.9 → Consider asset value
         └── CVSS <7.0 → Schedule for later
```

---

## 7. Exceptional Conditions (A10 - New)

### Fail-Open vs Fail-Closed

| Scenario | Fail-Open (BAD) | Fail-Closed (GOOD) |
|----------|-----------------|---------------------|
| Auth error | Allow access | Deny access |
| Parsing fails | Accept input | Reject input |
| Timeout | Retry forever | Limit + abort |

### What to Check

- Exception handlers that catch-all and ignore
- Missing error handling on security operations
- Race conditions in auth/authz
- Resource exhaustion scenarios

---

## 8. Scanning Methodology

### Phase-Based Approach

```
1. RECONNAISSANCE
   └── Understand the target
       ├── Technology stack
       ├── Entry points
       └── Data flows

2. DISCOVERY
   └── Identify potential issues
       ├── Configuration review
       ├── Dependency analysis
       └── Code pattern search

3. ANALYSIS
   └── Validate and prioritize
       ├── False positive elimination
       ├── Risk scoring
       └── Attack chain mapping

4. REPORTING
   └── Actionable findings
       ├── Clear reproduction steps
       ├── Business impact
       └── Remediation guidance
```

---

## 9. Code Pattern Analysis

### High-Risk Patterns

| Pattern | Risk | Look For |
|---------|------|----------|
| **String concat in queries** | Injection | `"SELECT * FROM " + user_input` |
| **Dynamic code execution** | RCE | `eval()`, `exec()`, `Function()` |
| **Unsafe deserialization** | RCE | `pickle.loads()`, `unserialize()` |
| **Path manipulation** | Traversal | User input in file paths |
| **Disabled security** | Various | `verify=False`, `--insecure` |

### Secret Patterns

| Type | Indicators |
|------|-----------|
| API Keys | `api_key`, `apikey`, high entropy |
| Tokens | `token`, `bearer`, `jwt` |
| Credentials | `password`, `secret`, `key` |
| Cloud | `AWS_`, `AZURE_`, `GCP_` prefixes |

---

## 10. Cloud Security Considerations

### Shared Responsibility

| Layer | You Own | Provider Owns |
|-------|---------|---------------|
| Data | ✅ | ❌ |
| Application | ✅ | ❌ |
| OS/Runtime | Depends | Depends |
| Infrastructure | ❌ | ✅ |

### Cloud-Specific Checks

- IAM: Least privilege applied?
- Storage: Public buckets?
- Network: Security groups tightened?
- Secrets: Using secrets manager?

---

## 11. Anti-Patterns

| ❌ Don't | ✅ Do |
|----------|-------|
| Scan without understanding | Map attack surface first |
| Alert on every CVE | Prioritize by exploitability + asset |
| Ignore false positives | Maintain verified baseline |
| Fix symptoms only | Address root causes |
| Scan once before deploy | Continuous scanning |
| Trust third-party deps blindly | Verify integrity, audit code |

---

## 12. Reporting Principles

### Finding Structure

Each finding should answer:
1. **What?** - Clear vulnerability description
2. **Where?** - Exact location (file, line, endpoint)
3. **Why?** - Root cause explanation
4. **Impact?** - Business consequence
5. **How to fix?** - Specific remediation

### Severity Classification

| Severity | Criteria |
|----------|----------|
| **Critical** | RCE, auth bypass, mass data exposure |
| **High** | Data exposure, privilege escalation |
| **Medium** | Limited scope, requires conditions |
| **Low** | Informational, best practice |

---

> **Remember:** Vulnerability scanning finds issues. Expert thinking prioritizes what matters. Always ask: "What would an attacker do with this?"

---
> Source: [threatpatrols/hibp-downloader](https://github.com/threatpatrols/hibp-downloader) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
