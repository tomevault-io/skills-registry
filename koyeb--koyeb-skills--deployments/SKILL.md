---
name: deployments
description: Manage Koyeb deployments: cancel, describe, get, list, and view logs. Use when the user needs deployment status or troubleshooting. Use when this capability is needed.
metadata:
  author: koyeb
---

# Koyeb Deployments

## When to use

Use this skill to inspect or manage deployment history, logs, and cancellation.

## Prerequisites

- Koyeb CLI installed and on PATH
- Authenticated session (see references/koyeb-auth.md)

## Workflow

1. Identify the service and deployment to inspect.
2. Use list/get/describe to capture details.
3. Read logs or cancel if requested.

## Commands

- List deployments: `koyeb deployments list [flags]`
- Get deployment: `koyeb deployments get <deployment-name>`
- Describe deployment: `koyeb deployments describe <deployment-name>`
- Deployment logs: `koyeb deployments logs <deployment-name>`
- Cancel deployment: `koyeb deployments cancel <deployment-name>`

## Examples

- List deployments: `koyeb deployments list --service my-service`
- Cancel a deployment: `koyeb deployments cancel <deployment-id>`
- Show build logs: `koyeb deployments logs <deployment-id> --type build`

## References

For detailed flags, see references/koyeb-deployments-flags.md.

- references/koyeb-cli.md
- references/koyeb-deployments-flags.md
- references/koyeb-auth.md
- references/koyeb-output.md
- scripts/koyeb-cli.sh

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/koyeb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
