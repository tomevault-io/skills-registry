---
name: wiz
description: Wiz cloud security platform for CNAPP, CSPM, CWPP, vulnerability management, and cloud detection/response. Use when working with Wiz APIs, GraphQL queries, analyzing cloud security posture, container security, or integrating Wiz findings into security workflows. Use when this capability is needed.
metadata:
  author: rthexton1964
---

# Wiz Cloud Security Platform

## Overview

Wiz CNAPP platform providing agentless cloud security across CSPM, CWPP, CIEM, and vulnerability management.

## Capabilities

| Module | Purpose |
|--------|---------|
| CSPM | Cloud misconfigurations |
| CWPP | Workload protection |
| CIEM | Cloud identity entitlements |
| Vulnerability Management | CVE scanning |
| DSPM | Data security posture |
| CDR | Cloud detection & response |

## Authentication

```python
import requests

WIZ_API_URL = "https://api.us1.app.wiz.io/graphql"  # Adjust region as needed

def get_wiz_token(client_id: str, client_secret: str, 
                  auth_url: str = "https://auth.app.wiz.io/oauth/token") -> str:
    """Get Wiz API token."""
    response = requests.post(
        auth_url,
        headers={"Content-Type": "application/x-www-form-urlencoded"},
        data={
            "grant_type": "client_credentials",
            "client_id": client_id,
            "client_secret": client_secret,
            "audience": "wiz-api"
        }
    )
    return response.json()["access_token"]

def wiz_headers(token: str) -> dict:
    return {"Authorization": f"Bearer {token}", "Content-Type": "application/json"}
```

## GraphQL Queries

### Issues Query

```python
def get_issues(token: str, severity: list = None, status: list = None,
               limit: int = 100) -> list:
    """Query Wiz issues."""
    filters = []
    if severity:
        filters.append(f'severity: [{", ".join(severity)}]')
    if status:
        filters.append(f'status: [{", ".join(status)}]')
    
    filter_str = ", ".join(filters) if filters else ""
    
    query = f"""
    query IssuesQuery {{
        issues(first: {limit}, filterBy: {{{filter_str}}}) {{
            nodes {{
                id
                severity
                status
                title
                description
                createdAt
                resolvedAt
                entitySnapshot {{
                    name
                    type
                    cloudPlatform
                    subscriptionExternalId
                    region
                }}
                control {{
                    name
                    securitySubCategories {{
                        title
                    }}
                }}
            }}
        }}
    }}
    """
    
    response = requests.post(
        WIZ_API_URL,
        headers=wiz_headers(token),
        json={"query": query}
    )
    return response.json().get("data", {}).get("issues", {}).get("nodes", [])
```

### Vulnerabilities Query

```python
def get_vulnerabilities(token: str, cve_id: str = None, severity: list = None,
                        has_exploit: bool = None, limit: int = 100) -> list:
    """Query Wiz vulnerabilities."""
    filters = []
    if cve_id:
        filters.append(f'vulnerabilityExternalId: "{cve_id}"')
    if severity:
        filters.append(f'severity: [{", ".join(severity)}]')
    if has_exploit is not None:
        filters.append(f'hasExploit: {str(has_exploit).lower()}')
    
    filter_str = ", ".join(filters) if filters else ""
    
    query = f"""
    query VulnerabilitiesQuery {{
        vulnerabilityFindings(first: {limit}, filterBy: {{{filter_str}}}) {{
            nodes {{
                id
                name
                severity
                CVSSScore
                hasExploit
                hasCisaKevExploit
                fixedVersion
                installedVersion
                package
                vulnerability {{
                    name
                    description
                    CVSSScore
                }}
                asset {{
                    name
                    type
                    cloudPlatform
                }}
            }}
        }}
    }}
    """
    
    response = requests.post(
        WIZ_API_URL,
        headers=wiz_headers(token),
        json={"query": query}
    )
    return response.json().get("data", {}).get("vulnerabilityFindings", {}).get("nodes", [])
```

### Cloud Resources Query

```python
def get_cloud_resources(token: str, resource_type: str = None,
                        cloud_platform: str = None, limit: int = 100) -> list:
    """Query cloud resources."""
    filters = []
    if resource_type:
        filters.append(f'type: ["{resource_type}"]')
    if cloud_platform:
        filters.append(f'cloudPlatform: [{cloud_platform}]')
    
    filter_str = ", ".join(filters) if filters else ""
    
    query = f"""
    query CloudResourcesQuery {{
        cloudResources(first: {limit}, filterBy: {{{filter_str}}}) {{
            nodes {{
                id
                name
                type
                cloudPlatform
                subscriptionExternalId
                region
                status
                tags {{
                    key
                    value
                }}
                securityState
            }}
        }}
    }}
    """
    
    response = requests.post(
        WIZ_API_URL,
        headers=wiz_headers(token),
        json={"query": query}
    )
    return response.json().get("data", {}).get("cloudResources", {}).get("nodes", [])
```

### Container Images Query

```python
def get_container_images(token: str, has_vulnerabilities: bool = True,
                         limit: int = 100) -> list:
    """Query container images."""
    query = f"""
    query ContainerImagesQuery {{
        containerImages(first: {limit}, filterBy: {{hasVulnerabilities: {str(has_vulnerabilities).lower()}}}) {{
            nodes {{
                id
                name
                registry
                repository
                tag
                digest
                criticalVulnerabilityCount
                highVulnerabilityCount
                mediumVulnerabilityCount
                lowVulnerabilityCount
                deployedTo {{
                    nodes {{
                        name
                        type
                    }}
                }}
            }}
        }}
    }}
    """
    
    response = requests.post(
        WIZ_API_URL,
        headers=wiz_headers(token),
        json={"query": query}
    )
    return response.json().get("data", {}).get("containerImages", {}).get("nodes", [])
```

### Security Graph Query

```python
def get_attack_paths(token: str, limit: int = 50) -> list:
    """Query attack paths."""
    query = f"""
    query AttackPathsQuery {{
        attackPaths(first: {limit}) {{
            nodes {{
                id
                name
                riskFactors
                severity
                steps {{
                    entitySnapshot {{
                        name
                        type
                    }}
                }}
            }}
        }}
    }}
    """
    
    response = requests.post(
        WIZ_API_URL,
        headers=wiz_headers(token),
        json={"query": query}
    )
    return response.json().get("data", {}).get("attackPaths", {}).get("nodes", [])
```

## Common Filters

```python
# Severity levels
SEVERITIES = ["CRITICAL", "HIGH", "MEDIUM", "LOW", "INFORMATIONAL"]

# Issue statuses
STATUSES = ["OPEN", "IN_PROGRESS", "RESOLVED", "REJECTED"]

# Cloud platforms
CLOUDS = ["AWS", "AZURE", "GCP", "ALIBABA", "OCI"]

# Resource types
RESOURCE_TYPES = [
    "VIRTUAL_MACHINE", "CONTAINER", "SERVERLESS",
    "STORAGE_BUCKET", "DATABASE", "LOAD_BALANCER",
    "SECURITY_GROUP", "IAM_ROLE", "IAM_USER"
]
```

## Issue Resolution

```python
def update_issue_status(token: str, issue_id: str, status: str,
                        note: str = None) -> dict:
    """Update issue status."""
    mutation = """
    mutation UpdateIssueStatus($input: UpdateIssueInput!) {
        updateIssue(input: $input) {
            issue {
                id
                status
            }
        }
    }
    """
    
    variables = {
        "input": {
            "id": issue_id,
            "status": status
        }
    }
    if note:
        variables["input"]["note"] = note
    
    response = requests.post(
        WIZ_API_URL,
        headers=wiz_headers(token),
        json={"query": mutation, "variables": variables}
    )
    return response.json()
```

## Reports

```python
def get_security_score(token: str) -> dict:
    """Get organization security score."""
    query = """
    query SecurityScoreQuery {
        securityScore {
            score
            trend
            categories {
                name
                score
                weight
            }
        }
    }
    """
    
    response = requests.post(
        WIZ_API_URL,
        headers=wiz_headers(token),
        json={"query": query}
    )
    return response.json().get("data", {}).get("securityScore", {})
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rthexton1964) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
