---
name: sailpoint
description: SailPoint Identity Governance and Administration (IGA) including IdentityNow (SaaS) and IdentityIQ (on-prem). Use when working with SailPoint APIs, identity lifecycle management, access certifications, role management, provisioning, or integrating SailPoint with security workflows. Use when this capability is needed.
metadata:
  author: rthexton1964
---

# SailPoint Identity Governance

## Overview

SailPoint IGA platform for identity lifecycle, access governance, and compliance.

## Products

| Product | Type | API |
|---------|------|-----|
| IdentityNow | SaaS | REST v3/beta |
| IdentityIQ | On-Prem | REST/SCIM |

## IdentityNow Authentication

```python
import requests
from base64 import b64encode

def get_identitynow_token(tenant: str, client_id: str, client_secret: str) -> str:
    """Get IdentityNow OAuth token."""
    auth = b64encode(f"{client_id}:{client_secret}".encode()).decode()
    response = requests.post(
        f"https://{tenant}.api.identitynow.com/oauth/token",
        headers={"Authorization": f"Basic {auth}", "Content-Type": "application/x-www-form-urlencoded"},
        data={"grant_type": "client_credentials"}
    )
    return response.json()["access_token"]

def idn_headers(token: str) -> dict:
    return {"Authorization": f"Bearer {token}", "Content-Type": "application/json"}
```

## Identity Management

```python
def search_identities(tenant: str, token: str, query: str, limit: int = 250) -> list:
    """Search identities using query DSL."""
    return requests.post(
        f"https://{tenant}.api.identitynow.com/v3/search",
        headers=idn_headers(token),
        json={
            "indices": ["identities"],
            "query": {"query": query},
            "sort": ["name"],
            "limit": limit
        }
    ).json()

def get_identity(tenant: str, token: str, identity_id: str) -> dict:
    """Get identity details."""
    return requests.get(
        f"https://{tenant}.api.identitynow.com/v3/identities/{identity_id}",
        headers=idn_headers(token)
    ).json()

def get_identity_access(tenant: str, token: str, identity_id: str) -> list:
    """Get all access for an identity."""
    return requests.get(
        f"https://{tenant}.api.identitynow.com/v3/identities/{identity_id}/access",
        headers=idn_headers(token)
    ).json()

# Search query examples
ACTIVE_EMPLOYEES = "attributes.employeeStatus:Active"
MANAGERS = "attributes.isManager:true"
BY_DEPARTMENT = 'attributes.department:"Engineering"'
PRIVILEGED_USERS = "access.type:ROLE AND access.name:*Admin*"
```

## Access Request Management

```python
def create_access_request(tenant: str, token: str, requested_for: str,
                          requested_items: list, comment: str = None) -> dict:
    """Submit access request."""
    body = {
        "requestedFor": [requested_for],
        "requestType": "GRANT_ACCESS",
        "requestedItems": requested_items
    }
    if comment:
        body["comment"] = comment
    
    return requests.post(
        f"https://{tenant}.api.identitynow.com/v3/access-requests",
        headers=idn_headers(token),
        json=body
    ).json()

def get_pending_requests(tenant: str, token: str, requested_for: str = None) -> list:
    """Get pending access requests."""
    params = {"requested-for": requested_for} if requested_for else {}
    return requests.get(
        f"https://{tenant}.api.identitynow.com/v3/access-requests/pending",
        headers=idn_headers(token),
        params=params
    ).json()

# Example access request item
ACCESS_REQUEST_ITEM = {
    "type": "ROLE",
    "id": "role-id-123",
    "comment": "Need for project X"
}
```

## Access Certifications

```python
def get_campaigns(tenant: str, token: str, status: str = None) -> list:
    """List certification campaigns."""
    params = {"filters": f"status eq \"{status}\""} if status else {}
    return requests.get(
        f"https://{tenant}.api.identitynow.com/v3/campaigns",
        headers=idn_headers(token),
        params=params
    ).json()

def get_certification_items(tenant: str, token: str, campaign_id: str,
                            reviewer_id: str = None) -> list:
    """Get items to be certified."""
    params = {}
    if reviewer_id:
        params["filters"] = f"reviewedBy eq \"{reviewer_id}\""
    
    return requests.get(
        f"https://{tenant}.api.identitynow.com/v3/campaigns/{campaign_id}/access-review-items",
        headers=idn_headers(token),
        params=params
    ).json()

def certify_item(tenant: str, token: str, item_id: str, 
                 decision: str, comment: str = None) -> dict:
    """Certify access item. decision: APPROVE, REVOKE"""
    body = {"decision": decision}
    if comment:
        body["comment"] = comment
    
    return requests.post(
        f"https://{tenant}.api.identitynow.com/v3/access-review-items/{item_id}/certify",
        headers=idn_headers(token),
        json=body
    ).json()
```

## Role Management

```python
def get_roles(tenant: str, token: str, filters: str = None) -> list:
    """List roles."""
    params = {"filters": filters} if filters else {}
    return requests.get(
        f"https://{tenant}.api.identitynow.com/v3/roles",
        headers=idn_headers(token),
        params=params
    ).json()

def create_role(tenant: str, token: str, name: str, description: str,
                owner_id: str, entitlements: list = None, 
                membership_criteria: dict = None) -> dict:
    """Create a new role."""
    body = {
        "name": name,
        "description": description,
        "owner": {"type": "IDENTITY", "id": owner_id},
        "requestable": True
    }
    if entitlements:
        body["entitlements"] = entitlements
    if membership_criteria:
        body["membership"] = {"type": "STANDARD", "criteria": membership_criteria}
    
    return requests.post(
        f"https://{tenant}.api.identitynow.com/v3/roles",
        headers=idn_headers(token),
        json=body
    ).json()
```

## Provisioning & Accounts

```python
def get_accounts(tenant: str, token: str, identity_id: str = None,
                 source_id: str = None) -> list:
    """List provisioned accounts."""
    filters = []
    if identity_id:
        filters.append(f'identityId eq "{identity_id}"')
    if source_id:
        filters.append(f'sourceId eq "{source_id}"')
    
    params = {"filters": " and ".join(filters)} if filters else {}
    return requests.get(
        f"https://{tenant}.api.identitynow.com/v3/accounts",
        headers=idn_headers(token),
        params=params
    ).json()

def disable_account(tenant: str, token: str, account_id: str) -> dict:
    """Disable an account."""
    return requests.post(
        f"https://{tenant}.api.identitynow.com/v3/accounts/{account_id}/disable",
        headers=idn_headers(token)
    ).json()

def enable_account(tenant: str, token: str, account_id: str) -> dict:
    """Enable an account."""
    return requests.post(
        f"https://{tenant}.api.identitynow.com/v3/accounts/{account_id}/enable",
        headers=idn_headers(token)
    ).json()
```

## Source (Connector) Management

```python
def get_sources(tenant: str, token: str) -> list:
    """List connected sources."""
    return requests.get(
        f"https://{tenant}.api.identitynow.com/v3/sources",
        headers=idn_headers(token)
    ).json()

def test_source_connection(tenant: str, token: str, source_id: str) -> dict:
    """Test source connectivity."""
    return requests.post(
        f"https://{tenant}.api.identitynow.com/v3/sources/{source_id}/test-connection",
        headers=idn_headers(token)
    ).json()

def aggregate_source(tenant: str, token: str, source_id: str, 
                     disable_optimization: bool = False) -> dict:
    """Trigger source aggregation."""
    return requests.post(
        f"https://{tenant}.api.identitynow.com/v3/sources/{source_id}/load-accounts",
        headers=idn_headers(token),
        json={"disableOptimization": disable_optimization}
    ).json()
```

## Separation of Duties (SOD)

```python
def get_sod_policies(tenant: str, token: str) -> list:
    """List SOD policies."""
    return requests.get(
        f"https://{tenant}.api.identitynow.com/v3/sod-policies",
        headers=idn_headers(token)
    ).json()

def check_sod_violations(tenant: str, token: str, identity_id: str) -> list:
    """Check SOD violations for identity."""
    return requests.post(
        f"https://{tenant}.api.identitynow.com/v3/sod-violations/check",
        headers=idn_headers(token),
        json={"identityId": identity_id}
    ).json()
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rthexton1964) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
