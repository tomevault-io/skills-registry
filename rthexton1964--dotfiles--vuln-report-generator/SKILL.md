---
name: vuln-report-generator
description: Generate professional vulnerability assessment reports from multiple data sources including CrowdStrike Spotlight, Wiz, Snyk, Qualys, Tenable, and CISA KEV. Use when creating executive summaries, technical vulnerability reports, remediation tracking documents, or compliance-ready vulnerability assessments. Use when this capability is needed.
metadata:
  author: rthexton1964
---

# Vulnerability Report Generator

## Overview

Generate standardized vulnerability reports from multiple security tool outputs with risk prioritization and remediation guidance.

## Report Types

| Type | Audience | Content |
|------|----------|---------|
| Executive Summary | Leadership | Risk overview, trends, key metrics |
| Technical Report | Security Team | Full vulnerability details, remediation |
| Remediation Tracker | IT Ops | Prioritized remediation tasks |

## Data Normalization Schema

```python
NORMALIZED_VULN = {
    "cve_id": str,
    "severity": str,           # CRITICAL, HIGH, MEDIUM, LOW
    "cvss_score": float,
    "epss_score": float,
    "is_kev": bool,
    "kev_due_date": str,
    "exploit_available": bool,
    "affected_asset": {"hostname": str, "ip": str, "criticality": str},
    "affected_software": {"product": str, "version": str},
    "remediation": {"action": str, "fixed_version": str},
    "source": str,
    "status": str
}
```

## Risk Scoring

```python
def calculate_risk_score(vuln: dict) -> float:
    """Calculate composite risk score (0-100)."""
    score = 0.0
    score += (vuln.get("cvss_score", 0) / 10) * 40      # CVSS: 40%
    score += vuln.get("epss_score", 0) * 20              # EPSS: 20%
    score += 15 if vuln.get("is_kev") else 0             # KEV: 15%
    score += 15 if vuln.get("exploit_available") else 0  # Exploit: 15%
    crit_weights = {"critical": 10, "high": 7, "medium": 4, "low": 1}
    score += crit_weights.get(vuln.get("affected_asset", {}).get("criticality", "medium"), 4)
    return min(score, 100)
```

## Source Normalizers

```python
def normalize_crowdstrike(data: list) -> list:
    """Normalize CrowdStrike Spotlight data."""
    return [{
        "cve_id": v.get("cve", {}).get("id"),
        "severity": v.get("cve", {}).get("severity", "").upper(),
        "cvss_score": v.get("cve", {}).get("base_score"),
        "is_kev": v.get("cve", {}).get("cisa_info", {}).get("is_cisa_kev", False),
        "exploit_available": v.get("cve", {}).get("exploit_status") == "available",
        "affected_asset": {"hostname": v.get("host_info", {}).get("hostname")},
        "affected_software": {"product": v.get("app", {}).get("product_name_version")},
        "source": "crowdstrike"
    } for v in data]

def normalize_wiz(data: list) -> list:
    """Normalize Wiz vulnerability data."""
    return [{
        "cve_id": v.get("name"),
        "severity": v.get("severity", "").upper(),
        "cvss_score": v.get("CVSSScore"),
        "is_kev": v.get("hasCisaKevExploit", False),
        "exploit_available": v.get("hasExploit", False),
        "affected_asset": {"hostname": v.get("asset", {}).get("name")},
        "remediation": {"fixed_version": v.get("fixedVersion")},
        "source": "wiz"
    } for v in data]
```

## Report Generation

Use the **docx skill** for Word documents, **xlsx skill** for spreadsheets, and **pdf skill** for PDF output.

### Executive Summary Structure
1. Risk Overview (severity distribution)
2. Key Metrics (total, KEV count, MTTR)
3. Top 10 Risks
4. Trend Analysis
5. Recommendations

### Technical Report Structure
1. Methodology
2. Findings by Severity
3. Affected Assets
4. Remediation Roadmap
5. Appendix: Full Details

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rthexton1964) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
