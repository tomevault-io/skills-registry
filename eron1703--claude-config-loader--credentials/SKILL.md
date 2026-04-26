---
name: credentials
description: Access team credentials stored in GitLab CI/CD variables - servers, databases, APIs Use when this capability is needed.
metadata:
  author: eron1703
---

# Credentials Management

**All team credentials are stored in GitLab CI/CD Variables.**

Access them using GitLab CLI - authenticates with your Microsoft SSO.

## Setup (One-time per developer)

```bash
# Install GitLab CLI
brew install glab

# Authenticate (opens browser for Microsoft SSO)
glab auth login
```

## Quick Access

```bash
# List all available credentials
glab variable list --group flow-master

# Get specific credential
glab variable get CREDENTIAL_NAME --group flow-master

# Copy to clipboard (macOS)
glab variable get CREDENTIAL_NAME --group flow-master | pbcopy

# Copy to clipboard (Linux)
glab variable get CREDENTIAL_NAME --group flow-master | xclip -selection clipboard
```

## Common Credentials

When team asks "what's the password for...":

```bash
# Database credentials
glab variable get PROD_DB_PASSWORD --group flow-master
glab variable get STAGING_DB_PASSWORD --group flow-master

# Server SSH keys
glab variable get SSH_PRIVATE_KEY --group flow-master
glab variable get BASTION_PASSWORD --group flow-master

# API tokens
glab variable get EXTERNAL_API_TOKEN --group flow-master
glab variable get SLACK_WEBHOOK_URL --group flow-master

# Hetzner Cloud (firewall, servers, DNS)
glab variable get HETZNER_API_TOKEN --group flow-master
```

### Hetzner Cloud API Token
- **Location**: GitLab CI/CD group variable `HETZNER_API_TOKEN` (masked, protected)
- **Group**: `flow-master`
- **Use for**: Managing server firewall rules, server operations
- **API endpoint**: `https://api.hetzner.cloud/v1/`
- **Auth header**: `Authorization: Bearer $HETZNER_API_TOKEN`
- **Firewall ID**: Use commander-mcp: `get_credential('HETZNER_API_TOKEN')`
- **Server IPs**: Use commander-mcp: `get_context_servers`

### Grafana Monitoring
- **Location**: GitLab CI/CD group variable `GRAFANA_ADMIN_PASSWORD` (masked, protected)
- **Group**: `flow-master`
- **URL variable**: `GRAFANA_URL` (not masked, not protected)
- **Dashboard**: dev-01 port 3002 — query commander-mcp for IP
- **Admin user**: `admin`
- **Password**: Use commander-mcp: `get_credential('GRAFANA_ADMIN_PASSWORD')`

### FlowMaster Dev Environment (flowmaster-dev namespace)
**Default credentials (NOT in GitLab - stored in database only):**

| Email | Role | Notes |
|-------|------|-------|
| `admin@flowmaster.ai` | Administrator | Created 2026-02-11, matches frontend dev login button |
| `admin@flowmaster.io` | Tenant Admin | Seeded from migration 012 |
| `superadmin@flowmaster.io` | Platform Superuser | Seeded from migration 012 |
| `eng.admin@flowmaster.io` | Org Admin | Seeded from migration 012 |

**Passwords**: Use commander-mcp: `get_credential('FM_DEV_ADMIN_PASSWORD')` or `get_credential('FM_DEV_SEED_PASSWORD')`

**Database details:**
- Database: `flowmaster_dev_core` (in databases-test namespace)
- Schema: `auth_service`
- Table: `"user"` (quoted - reserved keyword)

**Login endpoint:** dev-01 at `/api/v1/auth/login` — query commander-mcp for server IP

## Where are credentials stored?

**In GitLab UI:**
- **Group-level** (shared across projects): `Group > Settings > CI/CD > Variables`
- **Project-level** (specific to one project): `Project > Settings > CI/CD > Variables`

**Best practice:** Use group-level for shared credentials (databases, servers)

## Adding new credentials

```bash
# Add to group (requires Maintainer role)
glab variable set NEW_CREDENTIAL "value" --group flow-master

# Add to specific project
glab variable set NEW_CREDENTIAL "value" --repo your-org/project
```

## Security Notes

- ✅ All access is audited in GitLab
- ✅ Uses your Microsoft SSO (no additional password)
- ✅ Role-based access control
- ❌ Never commit credentials to git
- ❌ Never share credentials via Slack/email

## Troubleshooting

**"Variable not found":**
- Check if you have correct permissions in GitLab
- Verify variable name (case-sensitive)
- Confirm you're looking in correct group/project

**"Not authenticated":**
```bash
glab auth status  # Check auth status
glab auth login   # Re-authenticate
```

**List what you can access:**
```bash
glab variable list --group flow-master | grep KEY
```

> All credentials are available via commander-mcp: list_credentials, get_credential("name")

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eron1703) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
