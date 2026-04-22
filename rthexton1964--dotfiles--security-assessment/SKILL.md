---
name: security-assessment
description: Structured security assessment workflows for vulnerability assessments, penetration testing, security audits, and compliance reviews. Use when conducting security assessments, creating assessment scopes, documenting findings, generating remediation plans, or following security frameworks like NIST, CIS, ISO 27001. Use when this capability is needed.
metadata:
  author: rthexton1964
---

# Security Assessment Workflows

## Overview

Standardized workflows for conducting security assessments across vulnerability management, penetration testing, and compliance audits.

## Assessment Types

| Type | Scope | Output |
|------|-------|--------|
| Vulnerability Assessment | Infrastructure, applications | Prioritized findings, remediation plan |
| Penetration Test | Defined targets | Attack narrative, evidence, recommendations |
| Security Audit | Controls, policies | Compliance gaps, control effectiveness |
| Risk Assessment | Business processes | Risk register, treatment plan |

## Vulnerability Assessment Workflow

### Phase 1: Scoping

```python
SCOPE_TEMPLATE = {
    "assessment_name": str,
    "assessment_type": "vulnerability_assessment",
    "start_date": str,  # ISO date
    "end_date": str,
    "assessor": str,
    "stakeholders": list,
    "in_scope": {
        "ip_ranges": [],      # ["10.0.0.0/24", "192.168.1.0/24"]
        "hostnames": [],      # ["*.company.com"]
        "applications": [],   # ["App1", "App2"]
        "cloud_accounts": [], # ["AWS:123456789", "Azure:sub-id"]
        "exclusions": []      # Explicitly excluded targets
    },
    "data_sources": [],       # ["crowdstrike", "wiz", "snyk"]
    "scan_types": [],         # ["infrastructure", "container", "code"]
    "risk_rating_methodology": "CVSS+EPSS+KEV",
    "reporting_requirements": {
        "executive_summary": True,
        "technical_report": True,
        "remediation_tracker": True
    }
}
```

### Phase 2: Discovery & Scanning

```markdown
1. **Asset Discovery**
   - Pull asset inventory from Discover/EASM
   - Correlate with CMDB
   - Identify unmanaged assets
   
2. **Vulnerability Scanning**
   - Infrastructure: CrowdStrike Spotlight, Tenable, Qualys
   - Containers: Wiz, Snyk Container
   - Code: Snyk Code, SonarQube
   - Cloud: Prisma Cloud, Wiz CSPM
   
3. **Data Aggregation**
   - Normalize findings (see vuln-report-generator skill)
   - Deduplicate across sources
   - Enrich with KEV/EPSS (see cisa-kev-nvd skill)
```

### Phase 3: Analysis & Prioritization

```python
def prioritize_findings(vulns: list, asset_context: dict) -> list:
    """Prioritize vulnerabilities with business context."""
    for v in vulns:
        # Base risk score
        score = calculate_risk_score(v)
        
        # Asset context adjustments
        asset = v.get("affected_asset", {})
        hostname = asset.get("hostname", "")
        
        # Check business criticality
        if hostname in asset_context.get("critical_assets", []):
            score *= 1.5
        
        # Check internet exposure
        if hostname in asset_context.get("internet_facing", []):
            score *= 1.3
        
        # Check compensating controls
        if hostname in asset_context.get("isolated_network", []):
            score *= 0.8
        
        v["adjusted_risk_score"] = min(score, 100)
    
    return sorted(vulns, key=lambda x: x["adjusted_risk_score"], reverse=True)
```

### Phase 4: Reporting

See **vuln-report-generator** skill for report templates.

## Penetration Test Workflow

### Engagement Phases

```markdown
1. **Pre-Engagement**
   - Rules of engagement (ROE)
   - Scope definition
   - Authorization documentation
   - Emergency contacts
   
2. **Reconnaissance**
   - Passive: OSINT, DNS, certificate transparency
   - Active: Port scanning, service enumeration
   
3. **Vulnerability Analysis**
   - Automated scanning
   - Manual testing
   - Configuration review
   
4. **Exploitation**
   - Proof-of-concept development
   - Controlled exploitation
   - Evidence collection
   
5. **Post-Exploitation**
   - Privilege escalation
   - Lateral movement
   - Data access assessment
   
6. **Reporting**
   - Executive summary
   - Technical findings
   - Attack narrative
   - Remediation guidance
```

### Finding Documentation

```python
PENTEST_FINDING = {
    "id": str,
    "title": str,
    "severity": str,  # Critical, High, Medium, Low, Informational
    "cvss_score": float,
    "affected_systems": list,
    "description": str,
    "technical_details": str,
    "proof_of_concept": str,
    "evidence": [
        {"type": "screenshot", "path": str, "description": str},
        {"type": "log", "content": str}
    ],
    "business_impact": str,
    "remediation": {
        "short_term": str,
        "long_term": str,
        "effort": str
    },
    "references": list,  # CVEs, CWEs, external links
    "mitre_attack": list  # Technique IDs
}
```

## Compliance Assessment Workflow

### Framework Mapping

```python
FRAMEWORKS = {
    "NIST_CSF": {
        "domains": ["Identify", "Protect", "Detect", "Respond", "Recover"],
        "control_count": 108
    },
    "CIS_CONTROLS": {
        "version": "8",
        "implementation_groups": ["IG1", "IG2", "IG3"],
        "control_count": 18
    },
    "ISO_27001": {
        "version": "2022",
        "domains": 4,
        "control_count": 93
    },
    "SOC2": {
        "trust_criteria": ["Security", "Availability", "Processing Integrity", 
                          "Confidentiality", "Privacy"]
    },
    "PCI_DSS": {
        "version": "4.0",
        "requirements": 12
    }
}
```

### Control Assessment

```python
CONTROL_ASSESSMENT = {
    "control_id": str,
    "control_name": str,
    "framework": str,
    "assessment_status": str,  # Not Assessed, Compliant, Partially Compliant, Non-Compliant, N/A
    "evidence": list,
    "gaps": list,
    "compensating_controls": list,
    "remediation_plan": str,
    "owner": str,
    "due_date": str,
    "notes": str
}

def assess_control(control_id: str, evidence: list, criteria: dict) -> dict:
    """Assess control compliance."""
    met_criteria = sum(1 for c in criteria.values() if c.get("met"))
    total_criteria = len(criteria)
    
    if met_criteria == total_criteria:
        status = "Compliant"
    elif met_criteria > 0:
        status = "Partially Compliant"
    else:
        status = "Non-Compliant"
    
    return {
        "control_id": control_id,
        "assessment_status": status,
        "compliance_percentage": (met_criteria / total_criteria) * 100,
        "gaps": [k for k, v in criteria.items() if not v.get("met")],
        "evidence": evidence
    }
```

## Risk Assessment Workflow

### Risk Register Template

```python
RISK_ENTRY = {
    "risk_id": str,
    "risk_name": str,
    "description": str,
    "category": str,  # Technical, Operational, Compliance, Strategic
    "threat_source": str,
    "vulnerability": str,
    "asset_affected": str,
    "likelihood": int,  # 1-5
    "impact": int,      # 1-5
    "inherent_risk": int,  # likelihood * impact
    "existing_controls": list,
    "control_effectiveness": str,  # Effective, Partially Effective, Ineffective
    "residual_likelihood": int,
    "residual_impact": int,
    "residual_risk": int,
    "risk_treatment": str,  # Accept, Mitigate, Transfer, Avoid
    "treatment_plan": str,
    "risk_owner": str,
    "review_date": str
}

def calculate_risk_level(likelihood: int, impact: int) -> str:
    """Calculate risk level from likelihood and impact."""
    score = likelihood * impact
    if score >= 20:
        return "Critical"
    elif score >= 12:
        return "High"
    elif score >= 6:
        return "Medium"
    else:
        return "Low"
```

## Assessment Checklist

```markdown
### Pre-Assessment
- [ ] Scope document signed
- [ ] Authorization obtained
- [ ] Stakeholders identified
- [ ] Communication plan established
- [ ] Tools configured
- [ ] Data sources accessible

### During Assessment
- [ ] Discovery complete
- [ ] Scans executed
- [ ] Findings documented
- [ ] Evidence collected
- [ ] Initial prioritization done

### Post-Assessment
- [ ] Findings validated
- [ ] Risk scores calculated
- [ ] Reports generated
- [ ] Stakeholder review
- [ ] Remediation plan created
- [ ] Lessons learned documented
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rthexton1964) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
