---
name: crowdstrike-falcon
description: Complete CrowdStrike Falcon platform operations covering all modules: Falcon Prevent (EDR/AV), Spotlight (vulnerability management), EASM (external attack surface), Discover (asset inventory), Identity Protection, OverWatch (threat hunting), Intelligence, Insight XDR, LogScale, Horizon (CSPM), and Fusion SOAR. Use when working with CrowdStrike APIs, parsing Falcon data exports, building detections, analyzing incidents, vulnerability prioritization, or integrating Falcon with other security tools. Use when this capability is needed.
metadata:
  author: rthexton1964
---

# CrowdStrike Falcon Platform

## Overview

Complete reference for CrowdStrike Falcon platform operations. Covers API integration, data parsing, detection engineering, and cross-module workflows.

## Quick Reference

| Module | Primary Use | API Base |
|--------|-------------|----------|
| Falcon Prevent | EDR/AV, detections | `/detects/`, `/incidents/` |
| Spotlight | Vulnerability management | `/spotlight/` |
| EASM | External attack surface | `/fem/` |
| Discover | Asset inventory | `/discover/` |
| Identity | Identity protection | `/identity-protection/` |
| Horizon | Cloud security (CSPM) | `/cspm-registration/` |
| LogScale | Log management | `/humio/` |
| Intel | Threat intelligence | `/intel/` |
| Fusion | SOAR workflows | `/workflows/` |

## Authentication

```python
import requests

def get_falcon_token(client_id: str, client_secret: str, base_url: str = "https://api.crowdstrike.com") -> str:
    """Obtain OAuth2 token for Falcon API access."""
    response = requests.post(
        f"{base_url}/oauth2/token",
        data={"client_id": client_id, "client_secret": client_secret}
    )
    response.raise_for_status()
    return response.json()["access_token"]

def falcon_headers(token: str) -> dict:
    return {"Authorization": f"Bearer {token}", "Content-Type": "application/json"}
```

**Cloud regions:**
- US-1: `api.crowdstrike.com`
- US-2: `api.us-2.crowdstrike.com`
- EU-1: `api.eu-1.crowdstrike.com`
- US-GOV-1: `api.laggar.gcw.crowdstrike.com`

## Falcon Prevent (EDR)

### Detection Queries

```python
def get_detections(token: str, base_url: str, filter_query: str = None, limit: int = 100) -> list:
    """Query detections with FQL filter."""
    params = {"limit": limit}
    if filter_query:
        params["filter"] = filter_query
    
    # Get detection IDs
    resp = requests.get(
        f"{base_url}/detects/queries/detects/v1",
        headers=falcon_headers(token),
        params=params
    )
    detection_ids = resp.json().get("resources", [])
    
    if not detection_ids:
        return []
    
    # Get full details
    resp = requests.post(
        f"{base_url}/detects/entities/summaries/GET/v1",
        headers=falcon_headers(token),
        json={"ids": detection_ids}
    )
    return resp.json().get("resources", [])

# Common FQL filters
CRITICAL_DETECTIONS = "max_severity_displayname:'Critical'+status:'new'"
LAST_24H = "first_behavior:>='2024-01-01T00:00:00Z'"
RANSOMWARE = "behaviors.tactic:'Execution'+behaviors.technique:'Ransomware'"
```

### Incident Management

```python
def get_incidents(token: str, base_url: str, filter_query: str = None) -> list:
    """Query incidents with details."""
    params = {"filter": filter_query} if filter_query else {}
    
    resp = requests.get(
        f"{base_url}/incidents/queries/incidents/v1",
        headers=falcon_headers(token),
        params=params
    )
    incident_ids = resp.json().get("resources", [])
    
    if not incident_ids:
        return []
    
    resp = requests.post(
        f"{base_url}/incidents/entities/incidents/GET/v1",
        headers=falcon_headers(token),
        json={"ids": incident_ids}
    )
    return resp.json().get("resources", [])

def update_incident_status(token: str, base_url: str, incident_ids: list, status: str) -> dict:
    """Update incident status. Valid: new, in_progress, closed, reopened"""
    return requests.patch(
        f"{base_url}/incidents/entities/incident-actions/v1",
        headers=falcon_headers(token),
        json={"action_parameters": [{"name": "update_status", "value": status}], "ids": incident_ids}
    ).json()
```

### Real-Time Response (RTR)

```python
def init_rtr_session(token: str, base_url: str, device_id: str) -> str:
    """Initialize RTR session on endpoint."""
    resp = requests.post(
        f"{base_url}/real-time-response/entities/sessions/v1",
        headers=falcon_headers(token),
        json={"device_id": device_id, "queue_offline": True}
    )
    return resp.json()["resources"][0]["session_id"]

def execute_rtr_command(token: str, base_url: str, session_id: str, 
                        base_command: str, command_string: str) -> dict:
    """Execute RTR command. base_command: ls, cd, cat, ps, netstat, etc."""
    return requests.post(
        f"{base_url}/real-time-response/entities/command/v1",
        headers=falcon_headers(token),
        json={
            "session_id": session_id,
            "base_command": base_command,
            "command_string": command_string
        }
    ).json()
```

## Spotlight (Vulnerability Management)

### Vulnerability Queries

```python
def get_spotlight_vulnerabilities(token: str, base_url: str, 
                                   filter_query: str = None, facet: list = None) -> dict:
    """Query Spotlight vulnerabilities with optional faceting."""
    params = {"filter": filter_query or "status:'open'"}
    if facet:
        params["facet"] = facet  # e.g., ["cve.severity", "host_info.hostname"]
    
    resp = requests.get(
        f"{base_url}/spotlight/combined/vulnerabilities/v1",
        headers=falcon_headers(token),
        params=params
    )
    return resp.json()

# Common Spotlight FQL filters
CRITICAL_VULNS = "cve.severity:'CRITICAL'+status:'open'"
EXPLOITED_VULNS = "cve.exploit_status:'available'+status:'open'"
KEV_VULNS = "cve.cisa_info.is_cisa_kev:true+status:'open'"
RECENT_VULNS = "created_timestamp:>='2024-01-01'"
BY_HOST = "host_info.hostname:'HOSTNAME'"
```

### Spotlight Data Export

```python
def export_spotlight_csv(vulnerabilities: list, output_path: str):
    """Export Spotlight data to CSV for reporting."""
    import csv
    
    fieldnames = [
        "cve_id", "severity", "cvss_score", "hostname", "os", 
        "app_name", "app_version", "exploit_status", "kev", 
        "created", "remediation"
    ]
    
    with open(output_path, 'w', newline='') as f:
        writer = csv.DictWriter(f, fieldnames=fieldnames)
        writer.writeheader()
        
        for vuln in vulnerabilities:
            cve = vuln.get("cve", {})
            host = vuln.get("host_info", {})
            app = vuln.get("app", {})
            
            writer.writerow({
                "cve_id": cve.get("id"),
                "severity": cve.get("severity"),
                "cvss_score": cve.get("base_score"),
                "hostname": host.get("hostname"),
                "os": host.get("os_version"),
                "app_name": app.get("product_name_version"),
                "app_version": app.get("version"),
                "exploit_status": cve.get("exploit_status"),
                "kev": cve.get("cisa_info", {}).get("is_cisa_kev", False),
                "created": vuln.get("created_timestamp"),
                "remediation": vuln.get("remediation", {}).get("action")
            })
```

## EASM (External Attack Surface)

```python
def get_easm_assets(token: str, base_url: str, asset_type: str = None) -> list:
    """Query external assets. Types: domain, ip, certificate, web_asset"""
    params = {}
    if asset_type:
        params["filter"] = f"asset_type:'{asset_type}'"
    
    resp = requests.get(
        f"{base_url}/fem/queries/external-assets/v1",
        headers=falcon_headers(token),
        params=params
    )
    asset_ids = resp.json().get("resources", [])
    
    if not asset_ids:
        return []
    
    resp = requests.post(
        f"{base_url}/fem/entities/external-assets/v1",
        headers=falcon_headers(token),
        json={"ids": asset_ids}
    )
    return resp.json().get("resources", [])

def get_easm_exposures(token: str, base_url: str) -> list:
    """Get external exposures/vulnerabilities."""
    resp = requests.get(
        f"{base_url}/fem/queries/external-exposures/v1",
        headers=falcon_headers(token)
    )
    return resp.json().get("resources", [])
```

## Discover (Asset Inventory)

```python
def get_discover_hosts(token: str, base_url: str, filter_query: str = None) -> list:
    """Query Discover asset inventory."""
    params = {"filter": filter_query} if filter_query else {}
    
    resp = requests.get(
        f"{base_url}/discover/queries/hosts/v1",
        headers=falcon_headers(token),
        params=params
    )
    host_ids = resp.json().get("resources", [])
    
    if not host_ids:
        return []
    
    resp = requests.post(
        f"{base_url}/discover/entities/hosts/v1",
        headers=falcon_headers(token),
        json={"ids": host_ids}
    )
    return resp.json().get("resources", [])

# Common Discover filters
UNMANAGED = "entity_type:'unmanaged'"
MANAGED = "entity_type:'managed'"  
IOT_DEVICES = "entity_type:'iot'"
BY_SUBNET = "local_ip_addresses:'10.0.1.*'"
```

## Horizon (CSPM)

```python
def get_cspm_findings(token: str, base_url: str, cloud_provider: str = None) -> list:
    """Query cloud security posture findings."""
    params = {}
    if cloud_provider:
        params["filter"] = f"cloud_provider:'{cloud_provider}'"  # aws, azure, gcp
    
    resp = requests.get(
        f"{base_url}/cspm-registration/entities/policy-details/v1",
        headers=falcon_headers(token),
        params=params
    )
    return resp.json().get("resources", [])

def get_cloud_accounts(token: str, base_url: str) -> list:
    """List registered cloud accounts."""
    resp = requests.get(
        f"{base_url}/cspm-registration/entities/accounts/v1",
        headers=falcon_headers(token)
    )
    return resp.json().get("resources", [])
```

## Intel (Threat Intelligence)

```python
def search_intel_actors(token: str, base_url: str, query: str) -> list:
    """Search threat actors."""
    resp = requests.get(
        f"{base_url}/intel/queries/actors/v1",
        headers=falcon_headers(token),
        params={"q": query}
    )
    actor_ids = resp.json().get("resources", [])
    
    if not actor_ids:
        return []
    
    resp = requests.post(
        f"{base_url}/intel/entities/actors/v1",
        headers=falcon_headers(token),
        json={"ids": actor_ids}
    )
    return resp.json().get("resources", [])

def get_intel_indicators(token: str, base_url: str, indicator_type: str = None) -> list:
    """Get threat indicators. Types: hash_md5, hash_sha256, domain, ip_address, url"""
    params = {}
    if indicator_type:
        params["filter"] = f"type:'{indicator_type}'"
    
    resp = requests.get(
        f"{base_url}/intel/queries/indicators/v1",
        headers=falcon_headers(token),
        params=params
    )
    return resp.json().get("resources", [])
```

## LogScale (Humio)

```python
def query_logscale(token: str, base_url: str, repository: str, 
                   query: str, start: str = "24h", end: str = "now") -> dict:
    """Execute LogScale query."""
    return requests.post(
        f"{base_url}/humio/api/v1/repositories/{repository}/query",
        headers=falcon_headers(token),
        json={
            "queryString": query,
            "start": start,
            "end": end
        }
    ).json()

# Example LogScale queries
FAILED_LOGINS = 'event_simpleName=UserLogonFailed | groupBy([UserName, ComputerName])'
PROCESS_CREATION = 'event_simpleName=ProcessRollup2 | ImageFileName=/.*powershell.*/i'
NETWORK_CONNECTIONS = 'event_simpleName=NetworkConnectIP4 | RemotePort=443'
```

## Fusion (SOAR Workflows)

```python
def list_workflows(token: str, base_url: str) -> list:
    """List available Fusion workflows."""
    resp = requests.get(
        f"{base_url}/workflows/entities/definitions/v1",
        headers=falcon_headers(token)
    )
    return resp.json().get("resources", [])

def execute_workflow(token: str, base_url: str, definition_id: str, 
                     trigger_data: dict) -> dict:
    """Trigger a Fusion workflow."""
    return requests.post(
        f"{base_url}/workflows/entities/executions/v1",
        headers=falcon_headers(token),
        json={
            "definition_id": definition_id,
            "trigger": trigger_data
        }
    ).json()
```

## FQL (Falcon Query Language) Reference

### Operators
| Operator | Description | Example |
|----------|-------------|---------|
| `:` | Equals | `status:'open'` |
| `!:` | Not equals | `status!:'closed'` |
| `>`, `>=`, `<`, `<=` | Comparison | `cvss_score:>=7.0` |
| `+` | AND | `severity:'Critical'+status:'open'` |
| `,` | OR | `severity:'Critical',severity:'High'` |
| `*` | Wildcard | `hostname:'web-*'` |
| `~` | Contains | `description:~'ransomware'` |

### Common Patterns
```python
# Date ranges
"timestamp:>='2024-01-01T00:00:00Z'+timestamp:<='2024-01-31T23:59:59Z'"

# Multiple values
"severity:'Critical','High','Medium'"

# Nested fields
"behaviors.tactic:'Persistence'"

# Negation with wildcards
"hostname!:'test-*'"
```

## Data Parsing Utilities

See `references/data-formats.md` for detailed parsing of:
- Detection export JSON/CSV
- Spotlight vulnerability exports  
- Incident report formats
- LogScale query results
- RTR script outputs

## Rate Limits

| Endpoint Category | Requests/Minute |
|-------------------|-----------------|
| OAuth Token | 500 |
| Detections | 6000 |
| Incidents | 6000 |
| Spotlight | 6000 |
| RTR | 100 per session |
| Intel | 6000 |
| LogScale | Varies by repo |

Implement exponential backoff on 429 responses.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rthexton1964) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
