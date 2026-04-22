---
name: cyberark
description: CyberArk Privileged Access Management (PAM) including Vault, PVWA, PSM, CPM, CCP, Secrets Manager, and Identity Security. Use when working with CyberArk APIs, managing privileged accounts, rotating credentials, session management, or integrating CyberArk with security workflows. Use when this capability is needed.
metadata:
  author: rthexton1964
---

# CyberArk Privileged Access Management

## Overview

CyberArk PAM platform for securing, managing, and monitoring privileged access.

## Components

| Component | Purpose |
|-----------|---------|
| Vault | Secure credential storage |
| PVWA | Password Vault Web Access |
| PSM | Privileged Session Manager |
| CPM | Central Policy Manager |
| CCP | Central Credential Provider |
| Conjur | Secrets management for DevOps |

## Authentication

```python
import requests

def cyberark_login(base_url: str, username: str, password: str, 
                   auth_type: str = "CyberArk") -> str:
    """Authenticate to CyberArk PVWA. auth_type: CyberArk, LDAP, RADIUS"""
    response = requests.post(
        f"{base_url}/PasswordVault/API/auth/{auth_type}/Logon",
        json={"username": username, "password": password},
        verify=True
    )
    response.raise_for_status()
    return response.text.strip('"')

def cyberark_headers(token: str) -> dict:
    return {"Authorization": token, "Content-Type": "application/json"}

def cyberark_logout(base_url: str, token: str):
    """Terminate CyberArk session."""
    requests.post(
        f"{base_url}/PasswordVault/API/auth/Logoff",
        headers=cyberark_headers(token)
    )
```

## Account Management

```python
def get_accounts(base_url: str, token: str, search: str = None, 
                 safe: str = None, limit: int = 100) -> list:
    """Search for accounts in the vault."""
    params = {"limit": limit}
    if search:
        params["search"] = search
    if safe:
        params["filter"] = f"safeName eq {safe}"
    
    response = requests.get(
        f"{base_url}/PasswordVault/API/Accounts",
        headers=cyberark_headers(token),
        params=params
    )
    return response.json().get("value", [])

def get_account_password(base_url: str, token: str, account_id: str, 
                         reason: str = None) -> str:
    """Retrieve account password."""
    body = {"reason": reason} if reason else {}
    response = requests.post(
        f"{base_url}/PasswordVault/API/Accounts/{account_id}/Password/Retrieve",
        headers=cyberark_headers(token),
        json=body
    )
    return response.text.strip('"')

def add_account(base_url: str, token: str, safe_name: str, platform_id: str,
                name: str, address: str, username: str, password: str,
                properties: dict = None) -> dict:
    """Add a new privileged account."""
    body = {
        "safeName": safe_name,
        "platformId": platform_id,
        "name": name,
        "address": address,
        "userName": username,
        "secretType": "password",
        "secret": password
    }
    if properties:
        body["platformAccountProperties"] = properties
    
    return requests.post(
        f"{base_url}/PasswordVault/API/Accounts",
        headers=cyberark_headers(token),
        json=body
    ).json()

def change_password(base_url: str, token: str, account_id: str,
                    change_type: str = "change") -> dict:
    """Initiate password change. change_type: change, verify, reconcile"""
    endpoint_map = {
        "change": "Change",
        "verify": "Verify", 
        "reconcile": "Reconcile"
    }
    return requests.post(
        f"{base_url}/PasswordVault/API/Accounts/{account_id}/{endpoint_map[change_type]}",
        headers=cyberark_headers(token)
    ).json()
```

## Safe Management

```python
def get_safes(base_url: str, token: str, search: str = None) -> list:
    """List safes."""
    params = {"search": search} if search else {}
    response = requests.get(
        f"{base_url}/PasswordVault/API/Safes",
        headers=cyberark_headers(token),
        params=params
    )
    return response.json().get("value", [])

def create_safe(base_url: str, token: str, safe_name: str, 
                description: str = None, retention_days: int = 7) -> dict:
    """Create a new safe."""
    return requests.post(
        f"{base_url}/PasswordVault/API/Safes",
        headers=cyberark_headers(token),
        json={
            "safeName": safe_name,
            "description": description,
            "numberOfDaysRetention": retention_days,
            "managingCPM": "PasswordManager"
        }
    ).json()

def add_safe_member(base_url: str, token: str, safe_name: str, 
                    member_name: str, permissions: dict) -> dict:
    """Add member to safe with permissions."""
    return requests.post(
        f"{base_url}/PasswordVault/API/Safes/{safe_name}/Members",
        headers=cyberark_headers(token),
        json={
            "memberName": member_name,
            "permissions": permissions
        }
    ).json()
```

## Central Credential Provider (CCP)

```python
def ccp_get_password(ccp_url: str, app_id: str, safe: str, 
                     object_name: str = None, username: str = None,
                     address: str = None, cert_path: str = None) -> dict:
    """Retrieve credential via CCP (application authentication)."""
    params = {"AppID": app_id, "Safe": safe}
    if object_name:
        params["Object"] = object_name
    if username:
        params["UserName"] = username
    if address:
        params["Address"] = address
    
    response = requests.get(
        f"{ccp_url}/AIMWebService/api/Accounts",
        params=params,
        cert=cert_path,  # Client certificate auth
        verify=True
    )
    return response.json()

# Usage
# cred = ccp_get_password(
#     "https://ccp.company.com",
#     app_id="MyApp",
#     safe="Production-DB",
#     username="svc_database"
# )
# password = cred["Content"]
```

## Conjur Secrets Manager

```python
CONJUR_URL = "https://conjur.company.com"

def conjur_authenticate(account: str, login: str, api_key: str) -> str:
    """Authenticate to Conjur."""
    response = requests.post(
        f"{CONJUR_URL}/authn/{account}/{login}/authenticate",
        data=api_key,
        headers={"Accept-Encoding": "base64"}
    )
    return response.text

def conjur_get_secret(account: str, token: str, variable_id: str) -> str:
    """Retrieve secret from Conjur."""
    # URL encode the variable path
    encoded_id = requests.utils.quote(variable_id, safe='')
    response = requests.get(
        f"{CONJUR_URL}/secrets/{account}/variable/{encoded_id}",
        headers={"Authorization": f"Token token=\"{token}\""}
    )
    return response.text

# Conjur policy example
CONJUR_POLICY = '''
- !policy
  id: production/database
  body:
    - !variable password
    - !variable connection_string
    
    - !group consumers
    
    - !permit
      role: !group consumers
      privileges: [read, execute]
      resource: !variable password
'''
```

## Session Management (PSM)

```python
def get_psm_recordings(base_url: str, token: str, from_time: str = None,
                       to_time: str = None, safe: str = None) -> list:
    """List PSM session recordings."""
    params = {}
    if from_time:
        params["FromTime"] = from_time
    if to_time:
        params["ToTime"] = to_time
    if safe:
        params["Safe"] = safe
    
    return requests.get(
        f"{base_url}/PasswordVault/API/Recordings",
        headers=cyberark_headers(token),
        params=params
    ).json().get("Recordings", [])

def get_live_sessions(base_url: str, token: str) -> list:
    """Get active PSM sessions."""
    return requests.get(
        f"{base_url}/PasswordVault/API/LiveSessions",
        headers=cyberark_headers(token)
    ).json().get("LiveSessions", [])

def terminate_session(base_url: str, token: str, session_id: str) -> dict:
    """Terminate an active PSM session."""
    return requests.post(
        f"{base_url}/PasswordVault/API/LiveSessions/{session_id}/Terminate",
        headers=cyberark_headers(token)
    ).json()
```

## Common Permission Sets

```python
SAFE_PERMISSIONS = {
    "full_admin": {
        "useAccounts": True,
        "retrieveAccounts": True,
        "listAccounts": True,
        "addAccounts": True,
        "updateAccountContent": True,
        "updateAccountProperties": True,
        "deleteAccounts": True,
        "unlockAccounts": True,
        "manageSafe": True,
        "manageSafeMembers": True,
        "viewAuditLog": True,
        "viewSafeMembers": True
    },
    "user": {
        "useAccounts": True,
        "retrieveAccounts": True,
        "listAccounts": True
    },
    "auditor": {
        "listAccounts": True,
        "viewAuditLog": True,
        "viewSafeMembers": True
    }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rthexton1964) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
