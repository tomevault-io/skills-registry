---
name: deploy
description: Deploy code to VM environments via Docker Use when this capability is needed.
metadata:
  author: mycosoftlabs
---

## Deploy Skill

This skill handles deployment of code to VM environments.

### Steps:
1. SSH to target VM
2. Pull latest code from git
3. Rebuild Docker containers
4. Restart services
5. Verify health

### Required Tools:
- ssh_exec
- docker_exec
- health_check

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mycosoftlabs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
