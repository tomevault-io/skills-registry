---
name: querying-adme-entitlements
description: Queries Azure Data Manager for Energy (ADME) users and entitlements via OSDU API. Use when listing ADME users, checking roles (Viewer, Editor, Admin, Ops), resolving GUIDs to names, or troubleshooting ADME access. Use when this capability is needed.
metadata:
  author: danielscholl
---

# ADME Entitlements Operations

Reference documentation for querying ADME users and entitlements via the OSDU Entitlements API.

## Prerequisites

Verify environment variables are set before any operation:

```bash
echo "ADME_HOST: ${ADME_HOST:-NOT SET}"
echo "ADME_DATA_PARTITION: ${ADME_DATA_PARTITION:-NOT SET}"
echo "ADME_CLIENT: ${ADME_CLIENT:-NOT SET}"
echo "ADME_SECRET: ${ADME_SECRET:+SET}"
echo "ADME_TENANT_ID: ${ADME_TENANT_ID:-NOT SET}"
```

Test configuration and authentication:

```bash
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_manage.py check
```

## Quick Reference

### List Users and Roles

```bash
# List all ADME users with their roles
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_users.py

# JSON output for processing
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_users.py --json

# Filter to specific role
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_users.py --role Admin

# Skip GUID resolution (faster)
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_users.py --no-resolve
```

### Manage User Roles

```bash
# Add user as Viewer
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_manage.py add \
  --user "user@example.com" --role Viewer

# Add user as Editor with OWNER rights
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_manage.py add \
  --user "user@example.com" --role Editor --as-owner

# Remove from specific role
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_manage.py remove \
  --user "user@example.com" --role Viewer

# Remove from all roles
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_manage.py remove \
  --user "user@example.com" --all-roles
```

### Owner Management

Users in ADME groups have one of two membership types:
- **OWNER**: Can add/remove members from the group (management capability)
- **MEMBER**: Has access granted by the group (no management capability)

```bash
# Change user from MEMBER to OWNER (promote)
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_manage.py update \
  --user "user@example.com" --role Editor --to OWNER

# Change user from OWNER to MEMBER (demote)
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_manage.py update \
  --user "user@example.com" --role Editor --to MEMBER

# Remove an OWNER (blocked by default if last owner)
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_manage.py remove \
  --user "owner@example.com" --role Admin

# Force removal of last OWNER (use with caution!)
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_manage.py remove \
  --user "owner@example.com" --role Admin --force
```

**Last-Owner Protection**: By default, the script prevents removing or demoting the last OWNER of a group to avoid orphaning it. Use `--force` to override this protection when necessary.

## GUID Resolution

ADME stores users as GUIDs. The `adme_users.py` script automatically resolves GUIDs to human-readable names via Azure AD.

### How It Works

1. **Check cache** - Session-scoped cache prevents repeated lookups
2. **Try user** - Look up in Azure AD users
3. **Try service principal** - Check Azure AD service principals (prefixed with `[SP]`)
4. **Try application** - Check Azure AD applications (prefixed with `[App]`)

### Requirements

- Azure CLI authenticated: `az login`
- Permission to read Azure AD objects

### Performance

Use `--no-resolve` flag when:
- Processing large user lists
- Azure CLI not available
- Speed is more important than readability

```bash
# Fast mode without GUID resolution
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-adme/scripts/adme_users.py --no-resolve
```

## Role Groups

| Role | Group Pattern | Access Level |
|------|---------------|--------------|
| Viewer | `users.datalake.viewers@{partition}.{domain}` | Read-only |
| Editor | `users.datalake.editors@{partition}.{domain}` | Read-write |
| Admin | `users.datalake.admins@{partition}.{domain}` | Administrative |
| Ops | `users.datalake.ops@{partition}.{domain}` | Operations |
| Root | `users.data.root@{partition}.{domain}` | Superuser |

## Output Formats

| Format | Flag | Use Case |
|--------|------|----------|
| Table | (default) | Human readable with colors |
| JSON | `--json` | Automation and processing |

### JSON Output Structure

```json
{
  "success": true,
  "host": "adme.energy.azure.com",
  "partition": "opendes",
  "total_users": 5,
  "users": [
    {
      "email": "user@example.com",
      "display_name": "John Doe",
      "is_root": false,
      "roles": {"Viewer": "MEMBER", "Editor": "OWNER"}
    }
  ]
}
```

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `ADME_HOST` | Yes | ADME instance hostname |
| `ADME_DATA_PARTITION` | Yes | Data partition ID |
| `ADME_CLIENT` | Yes | App registration client ID |
| `ADME_SECRET` | Yes | App registration secret |
| `ADME_TENANT_ID` | Yes | Azure AD tenant ID |
| `ADME_DOMAIN` | No | Entitlements domain (default: `dataservices.energy`) |

## Error Handling

| Error | Cause | Solution |
|-------|-------|----------|
| 401 Unauthorized | Invalid/expired token | Check ADME_SECRET, re-authenticate |
| 403 Forbidden | Missing API permissions | Add Entitlements API permissions in ADME |
| 404 Not Found | Group/user not found | Verify group name format and partition ID |
| 409 Conflict | User already in group | User is already a member (not an error) |
| Last owner | Removing/demoting last OWNER | Add another OWNER first, or use `--force` |
| Missing env vars | Not configured | Set required ADME_* environment variables |
| GUID not resolved | Azure AD issue | Check `az login`, try `--no-resolve` |

## Best Practices

1. **Verify environment** - Run `check` command before operations
2. **Start with Viewer** - Assign least privilege, escalate as needed
3. **Use email addresses** - Easier to track than GUIDs
4. **Use --no-resolve for bulk** - Faster for large user lists
5. **Check Azure CLI auth** - GUID resolution requires `az login`

## Reference Files

- [reference/entitlements.md](reference/entitlements.md) - Full API reference
- [reference/troubleshooting.md](reference/troubleshooting.md) - Detailed error solutions
- [scripts/adme_users.py](scripts/adme_users.py) - User listing script
- [scripts/adme_manage.py](scripts/adme_manage.py) - User management script

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danielscholl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
