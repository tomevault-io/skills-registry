---
name: querying-azure-ad
description: Queries Azure AD/Entra ID users and groups using Azure CLI. Use when listing users, searching groups, checking memberships, or troubleshooting Azure AD operations. Use when this capability is needed.
metadata:
  author: danielscholl
---

# Azure AD/Entra ID Operations

Reference documentation for querying Azure AD users and groups via Azure CLI.

## Prerequisites

Verify Azure CLI authentication before any operation:

```bash
az account show --query "{name:name, user:user.name, tenantId:tenantId}" -o table
```

## Quick Reference

### User Queries

```bash
# List all users (table format)
az ad user list --query "[].{name:displayName, mail:mail, type:userType}" -o table

# Find specific user
az ad user show --id "user@example.com" -o table

# Filter guest users
az ad user list --filter "userType eq 'Guest'" -o table

# Search by name prefix
az ad user list --filter "startswith(displayName,'John')" -o table

# Count users
az ad user list --query "length(@)"
```

### Group Queries

```bash
# List all groups
az ad group list --query "[].{name:displayName, type:securityEnabled}" -o table

# Get group members
az ad group member list --group "GroupName" --query "[].{name:displayName, mail:mail}" -o table

# Get user's groups
az ad user get-member-groups --id "user@example.com" -o table

# Check membership
az ad group member check --group "GROUP_ID" --member-id "USER_ID"
```

### Group Management

```bash
# Add user to group (requires object IDs)
USER_ID=$(az ad user show --id "user@example.com" --query "id" -o tsv)
GROUP_ID=$(az ad group show --group "GroupName" --query "id" -o tsv)
az ad group member add --group "$GROUP_ID" --member-id "$USER_ID"
```

## OData Filter Syntax

| Filter | Example |
|--------|---------|
| Equals | `"mail eq 'user@example.com'"` |
| Starts with | `"startswith(displayName,'J')"` |
| User type | `"userType eq 'Guest'"` |
| Enabled | `"accountEnabled eq true"` |
| Security groups | `"securityEnabled eq true"` |

**Important:** OData functions are case-sensitive. Use `startswith`, not `StartsWith`.

## Output Formats

| Format | Flag | Use Case |
|--------|------|----------|
| Table | `-o table` | Human readable |
| JSON | `-o json` | Full details |
| TSV | `-o tsv` | Single values for variables |

## JMESPath Queries

```bash
# Select specific fields
--query "[].{name:displayName, email:mail}"

# Filter in query
--query "[?userType=='Guest']"

# Count results
--query "length(@)"
```

## Required Permissions

| Operation | Minimum Role |
|-----------|-------------|
| List users/groups | Directory Readers |
| Add to group | Groups Administrator |
| Invite guests | Guest Inviter |

## Error Handling

| Error | Cause | Solution |
|-------|-------|----------|
| `Authorization_RequestDenied` | Insufficient permissions | Request Directory Readers role |
| `Request_ResourceNotFound` | User/group not found | Verify spelling, use object ID |
| `Request_BadRequest` | Invalid filter | Check OData syntax, use lowercase functions |

## Guest User Invitation

Invite external users to your tenant using the `/ag1:invite` command.

### Usage

```bash
# Invite a guest user
/ag1:invite user@external.com

# Or use the script directly with options
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-ad/scripts/invite_guest.py invite --email user@external.com

# With custom options
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-ad/scripts/invite_guest.py invite \
  --email user@external.com \
  --redirect-url "https://myapp.com" \
  --message "Welcome to our organization!"

# Check authentication status
uv run ${CLAUDE_PLUGIN_ROOT}/skills/azure-ad/scripts/invite_guest.py check
```

### Required Permissions

| Operation | Permission | Role |
|-----------|-----------|------|
| Invite guests | User.Invite.All | Guest Inviter |

### Script Options

| Option | Description | Default |
|--------|-------------|---------|
| `--email` | Email address to invite (required) | - |
| `--redirect-url` | URL after accepting invitation | https://myapps.microsoft.com |
| `--send-email/--no-send-email` | Send invitation email | Yes |
| `--message` | Custom message in invitation | None |
| `--json` | Output as JSON | No |

## Reference Files

For detailed documentation:
- [reference/commands.md](reference/commands.md) - Full CLI command reference
- [reference/troubleshooting.md](reference/troubleshooting.md) - Detailed error solutions
- [scripts/invite_guest.py](scripts/invite_guest.py) - Guest invitation script

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danielscholl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
