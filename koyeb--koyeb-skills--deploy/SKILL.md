---
name: deploy
description: Deploy Koyeb services and apps. Use when the user asks to deploy code, trigger a redeploy, or roll out changes. Use when this capability is needed.
metadata:
  author: koyeb
---

# Koyeb Deploy

## When to use

Use this skill to deploy code or trigger redeploys for existing services.

## Prerequisites

- Koyeb CLI installed and on PATH
- Authenticated session (see references/koyeb-auth.md)

## Workflow

1. Identify target app and service.
2. If needed, create an archive (see archives skill).
3. Deploy or redeploy the service.
4. Verify via deployments list and logs.

## Commands

- Deploy a directory: `koyeb deploy <path> <app>/<service> [flags]`
- Redeploy service: `koyeb services redeploy <service-name> [flags]`
- List deployments: `koyeb deployments list --service <service-id-or-name>`
- Deployment logs: `koyeb deployments logs <deployment-id>`

## Examples

- Deploy a directory: `koyeb deploy ./dist my-app/my-service`
- Redeploy a service: `koyeb services redeploy my-service --app my-app`

## References

For detailed flags, see references/koyeb-deploy-flags.md.

- references/koyeb-cli.md
- references/koyeb-deploy-flags.md
- references/koyeb-auth.md
- references/koyeb-output.md
- scripts/koyeb-cli.sh

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/koyeb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
