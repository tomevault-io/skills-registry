---
name: osdu-preshipping
description: Provision users and service principals for OSDU preshipping environments. Use when setting up preshipping access, enabling preshipping for users or OIDs, bulk-provisioning for preshipping testing, or configuring pre-production OSDU environments. Use when this capability is needed.
metadata:
  author: danielscholl-osdu
---

# OSDU Preshipping Setup

## IMPORTANT: Intent Detection

Parse user input to determine intent:

| User Input | Intent | Action |
|------------|--------|--------|
| `help`, `how to`, `how do I`, `usage`, `format` | Help | Respond with usage info below |
| Contains email or OID + add/remove intent | Execute | Run preshipping script |

## Adding Users to Preshipping

### For New External Users (Recommended)

Use the `/invite` command with the `--preshipping` flag:

```
/invite user@company.com --preshipping
/invite user@company.com --groups "AzOSDUPreshipReaders" --preshipping
```

This handles tenant invitation, AD group membership, AND preshipping in one command.

### For Existing Tenant Users

If the user is already in your Azure AD tenant, just ask:

```
add user@example.com to preshipping
```

Or with a preview first:

```
add user@example.com to preshipping --dry-run
```

### What Gets Provisioned

Users are added as OWNER to all preshipping groups including:

| Category | Groups |
|----------|--------|
| Data Lake | `users@`, `users.datalake.ops@`, `users.datalake.admins@` |
| SDMS | `service.edsdms.user@`, `data.sdms.*` |
| Seismic | `seismic.default.*`, `seistore.system.admin@` |
| Secrets | `service.secret.admin@`, `service.secret.viewer@`, `service.secret.editor@` |
| Search | `service.search.admin@`, `service.search.user@` |
| Wellbore | `data.wellbore.owner@` |
| Reservoir | `service.reservoir-dms.*` |
| Delivery | `service.delivery.viewer@` |

### Workflow

1. Run `/audit <company>` to see existing users' preshipping setup
2. For new external users: `/invite user@company.com --preshipping`
3. For existing tenant users: "add user@company.com to preshipping"

---

## AI Execution (Internal)

When user requests preshipping actions, run these scripts:

```bash
# Add user
uv run .claude/skills/osdu-preshipping/scripts/preshipping.py add --user "EMAIL" [--dry-run]

# Add by OID
uv run .claude/skills/osdu-preshipping/scripts/preshipping.py add --oid "GUID" [--dry-run]

# Remove user
uv run .claude/skills/osdu-preshipping/scripts/preshipping.py remove --user "EMAIL"

# List groups
uv run .claude/skills/osdu-preshipping/scripts/preshipping.py list-groups

# Check config
uv run .claude/skills/osdu-preshipping/scripts/preshipping.py check
```

### Output Presentation

**Present the script output directly to the user.** Do NOT summarize.

---

## Prerequisites

Verify environment variables are set:

```bash
echo "AI_OSDU_HOST: ${AI_OSDU_HOST:-NOT SET}"
echo "AI_OSDU_DATA_PARTITION: ${AI_OSDU_DATA_PARTITION:-NOT SET}"
echo "AI_OSDU_CLIENT: ${AI_OSDU_CLIENT:-NOT SET}"
echo "AI_OSDU_SECRET: ${AI_OSDU_SECRET:+SET}"
echo "AI_OSDU_TENANT_ID: ${AI_OSDU_TENANT_ID:-NOT SET}"
```

Test configuration:

```bash
uv run .claude/skills/osdu-preshipping/scripts/preshipping.py check
```

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `AI_OSDU_HOST` | Yes | OSDU instance hostname |
| `AI_OSDU_DATA_PARTITION` | Yes | Data partition ID (e.g., `opendes`) |
| `AI_OSDU_CLIENT` | Yes | App registration client ID |
| `AI_OSDU_SECRET` | Yes | App registration secret |
| `AI_OSDU_TENANT_ID` | Yes | Azure AD tenant ID |
| `AI_OSDU_DOMAIN` | No | Entitlements domain (default from config) |

## Error Handling

| Error | Cause | Solution |
|-------|-------|----------|
| 401 Unauthorized | Invalid/expired token | Check AI_OSDU_SECRET |
| 403 Forbidden | Missing permissions | Verify app has entitlements API access |
| 409 Conflict | Already in group | Not an error, skipped |
| Missing env vars | Not configured | Set required AI_OSDU_* variables |

## Reference Files

- [reference/groups.md](reference/groups.md) - Complete group list with descriptions
- [scripts/preshipping.py](scripts/preshipping.py) - Provisioning script

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danielscholl-osdu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
