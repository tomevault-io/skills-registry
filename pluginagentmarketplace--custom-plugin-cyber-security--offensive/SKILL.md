---
name: offensive-security
description: Test authentication mechanisms Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Offensive Security Skill

> **Purpose**: Authorized security testing methodologies and techniques for identifying vulnerabilities.

## Atomic Operations

```yaml
┌─────────────────────────────────────────────────────────────────┐
│                    SKILL OPERATIONS                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐     │
│  │scan_vuln    │  │test_inject  │  │enumerate_services   │     │
│  │             │  │             │  │                     │     │
│  │ Input:      │  │ Input:      │  │ Input:              │     │
│  │  - target   │  │  - endpoint │  │  - host             │     │
│  │  - type     │  │  - type     │  │  - port_range       │     │
│  │  - depth    │  │  - payloads │  │                     │     │
│  │             │  │             │  │ Output:             │     │
│  │ Output:     │  │ Output:     │  │  - services[]       │     │
│  │  - vulns[]  │  │  - vuln     │  │  - os_detect        │     │
│  │  - time     │  │  - evidence │  │                     │     │
│  └─────────────┘  └─────────────┘  └─────────────────────┘     │
│                                                                 │
│  ┌─────────────────────────────────────────┐                   │
│  │ test_authentication                      │                   │
│  │                                          │                   │
│  │ Input: target, test_type                 │                   │
│  │ Output: findings[], weak_creds[]         │                   │
│  └─────────────────────────────────────────┘                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Testing Methodology

### OWASP Top 10 Coverage

| Vulnerability | Test Operation | Detection Method |
|---------------|----------------|------------------|
| A01 Broken Access Control | test_authentication | IDOR, privilege tests |
| A02 Cryptographic Failures | scan_vulnerability | TLS/cert analysis |
| A03 Injection | test_injection | SQLi, XSS, Command |
| A04 Insecure Design | manual | Architecture review |
| A05 Security Misconfig | enumerate_services | Header/config scan |
| A06 Vulnerable Components | scan_vulnerability | CVE database check |
| A07 Auth Failures | test_authentication | Session/token tests |
| A08 Data Integrity | scan_vulnerability | Deserialization checks |
| A09 Logging Failures | manual | Log analysis |
| A10 SSRF | test_injection | SSRF payload tests |

## Troubleshooting

### Debug Decision Tree

```
Scan Failed
    │
    ├─► E_NETWORK_TIMEOUT
    │   ├── Check: ping/traceroute target
    │   ├── Action: Increase timeout, use retry
    │   └── Escalate: If persistent, check firewall
    │
    ├─► E_RATE_LIMITED
    │   ├── Check: Response headers for rate info
    │   ├── Action: Apply exponential backoff
    │   └── Escalate: Reduce concurrency
    │
    ├─► E_WAF_BLOCKED
    │   ├── Check: Response body for WAF signature
    │   ├── Action: Modify payloads, encoding
    │   └── Escalate: Document WAF presence
    │
    └─► E_NO_AUTHORIZATION
        └── STOP: Cannot proceed without authorization
```

### Common Issues

| Issue | Symptom | Solution |
|-------|---------|----------|
| False positives | High vuln count | Verify manually, adjust sensitivity |
| Slow scans | Timeout errors | Reduce depth, batch targets |
| Missing vulns | Clean scan | Check scope, increase depth |
| WAF evasion | Blocked requests | Use encoding, timing techniques |

## Unit Test Template

```python
# tests/test_offensive_skill.py
import pytest
from skills.offensive import OffensiveSecurity

class TestVulnerabilityScan:
    def test_valid_web_target(self):
        skill = OffensiveSecurity()
        result = skill.scan_vulnerability(
            target="http://testsite.local",
            scan_type="web",
            depth="quick"
        )
        assert result.status == "success"
        assert isinstance(result.vulnerabilities, list)

    def test_invalid_target_format(self):
        skill = OffensiveSecurity()
        with pytest.raises(ValidationError) as exc:
            skill.scan_vulnerability(target="invalid!!!")
        assert exc.value.code == "E_INVALID_TARGET"

    def test_authorization_required(self):
        skill = OffensiveSecurity(authorization=None)
        with pytest.raises(AuthorizationError) as exc:
            skill.scan_vulnerability(target="http://target.com")
        assert exc.value.code == "E_NO_AUTHORIZATION"
```

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.0.0 | 2025-01-01 | Production-grade with atomic operations |
| 1.0.0 | 2024-12-29 | Initial release |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
