---
name: google-secops
description: Google Security Operations (Chronicle SIEM/SOAR) platform for threat detection, investigation, and response. Use when working with Chronicle APIs, UDM queries, YARA-L detection rules, SOAR playbooks, or integrating Google SecOps with other security tools. Covers Chronicle SIEM, Chronicle SOAR, VirusTotal integration, and Mandiant threat intelligence. Use when this capability is needed.
metadata:
  author: rthexton1964
---

# Google Security Operations (Chronicle)

## Overview

Google SecOps combines Chronicle SIEM, Chronicle SOAR, VirusTotal, and Mandiant intelligence for unified security operations.

## Quick Reference

| Component | Purpose | API/Interface |
|-----------|---------|---------------|
| Chronicle SIEM | Log ingestion, UDM search | Search API, Detection Engine |
| Chronicle SOAR | Playbook automation | SOAR API |
| VirusTotal | Malware/IOC analysis | VT API v3 |
| Mandiant Intel | Threat intelligence | Mandiant Advantage API |

## Authentication

```python
from google.oauth2 import service_account
from google.auth.transport.requests import Request

def get_chronicle_credentials(service_account_file: str) -> str:
    """Get Chronicle API credentials."""
    credentials = service_account.Credentials.from_service_account_file(
        service_account_file,
        scopes=["https://www.googleapis.com/auth/chronicle-backstory"]
    )
    credentials.refresh(Request())
    return credentials.token

def chronicle_headers(token: str) -> dict:
    return {"Authorization": f"Bearer {token}", "Content-Type": "application/json"}
```

## Chronicle SIEM

### UDM Search

```python
import requests

def search_udm(token: str, customer_id: str, query: str, 
               start_time: str, end_time: str) -> dict:
    """Execute UDM search query."""
    url = f"https://backstory.googleapis.com/v2/detect/rules/{customer_id}:searchUdmEvents"
    
    return requests.post(
        url,
        headers=chronicle_headers(token),
        json={
            "query": query,
            "timeRange": {
                "startTime": start_time,
                "endTime": end_time
            }
        }
    ).json()

# Example UDM queries
FAILED_LOGINS = '''
metadata.event_type = "USER_LOGIN"
AND security_result.action = "BLOCK"
'''

PROCESS_EXECUTION = '''
metadata.event_type = "PROCESS_LAUNCH"
AND target.process.file.full_path = /.*powershell.*/
'''

NETWORK_CONNECTIONS = '''
metadata.event_type = "NETWORK_CONNECTION"
AND target.port = 443
AND target.ip NOT IN cidr"10.0.0.0/8"
'''
```

### YARA-L Detection Rules

```yaml
rule suspicious_powershell_execution {
  meta:
    author = "Security Team"
    description = "Detects suspicious PowerShell execution patterns"
    severity = "HIGH"
    mitre_attack = "T1059.001"

  events:
    $process.metadata.event_type = "PROCESS_LAUNCH"
    $process.target.process.file.full_path = /.*powershell\.exe$/i
    $process.target.process.command_line = /.*(-enc|-encodedcommand|-e ).*/i

  condition:
    $process
}

rule lateral_movement_psexec {
  meta:
    author = "Security Team"
    description = "Detects potential lateral movement via PsExec"
    severity = "HIGH"
    mitre_attack = "T1570"

  events:
    $e1.metadata.event_type = "NETWORK_CONNECTION"
    $e1.target.port = 445
    $e2.metadata.event_type = "PROCESS_LAUNCH"
    $e2.target.process.file.full_path = /.*psexe.*/i
    $e1.principal.hostname = $e2.principal.hostname
    $e2.metadata.event_timestamp.seconds - $e1.metadata.event_timestamp.seconds < 300

  match:
    $e1.principal.hostname over 5m

  condition:
    $e1 and $e2
}
```

### Detection Rule Management

```python
def create_detection_rule(token: str, customer_id: str, rule_text: str, 
                          rule_name: str) -> dict:
    """Create a new YARA-L detection rule."""
    url = f"https://backstory.googleapis.com/v2/detect/rules"
    
    return requests.post(
        url,
        headers=chronicle_headers(token),
        json={
            "ruleText": rule_text,
            "ruleName": rule_name,
            "metadata": {"customer_id": customer_id}
        }
    ).json()

def list_detections(token: str, customer_id: str, 
                    start_time: str, end_time: str) -> dict:
    """List detection alerts."""
    url = f"https://backstory.googleapis.com/v2/detect/rules/{customer_id}:listDetections"
    
    return requests.get(
        url,
        headers=chronicle_headers(token),
        params={"pageSize": 1000, "startTime": start_time, "endTime": end_time}
    ).json()
```

## UDM Field Reference

| Field Path | Description |
|------------|-------------|
| `metadata.event_type` | Event category |
| `metadata.event_timestamp` | Event time |
| `principal.hostname` | Source host |
| `principal.user.userid` | Source user |
| `principal.ip` | Source IP |
| `target.hostname` | Destination host |
| `target.ip` | Destination IP |
| `target.port` | Destination port |
| `target.process.file.full_path` | Process path |
| `target.process.command_line` | Command line |
| `target.file.sha256` | File hash |
| `security_result.action` | Allow/Block |

## VirusTotal Integration

```python
VT_API_URL = "https://www.virustotal.com/api/v3"

def vt_lookup_hash(api_key: str, file_hash: str) -> dict:
    return requests.get(
        f"{VT_API_URL}/files/{file_hash}",
        headers={"x-apikey": api_key}
    ).json()

def vt_lookup_domain(api_key: str, domain: str) -> dict:
    return requests.get(
        f"{VT_API_URL}/domains/{domain}",
        headers={"x-apikey": api_key}
    ).json()
```

## Chronicle SOAR Playbooks

```python
def trigger_playbook(soar_url: str, api_key: str, playbook_id: str, 
                     case_id: str, inputs: dict = None) -> dict:
    """Trigger a SOAR playbook."""
    return requests.post(
        f"{soar_url}/api/v1/playbooks/{playbook_id}/execute",
        headers={"Authorization": f"Bearer {api_key}", "Content-Type": "application/json"},
        json={"caseId": case_id, "inputs": inputs or {}}
    ).json()
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rthexton1964) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
