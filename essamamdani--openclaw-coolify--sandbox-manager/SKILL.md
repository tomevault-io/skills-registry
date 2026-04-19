---
name: sandbox-manager
description: Create and manage isolated application sandboxes (Next.js, Python, PHP, etc.) with public URLs via Cloudflare. Use when this capability is needed.
metadata:
  author: essamamdani
---

# Sandbox Manager

This skill allows you to spin up isolated "sandboxes" for various application stacks. Each sandbox is a Docker container with:
- A persistent volume.
- A specific technology stack (Node/Bun, Python/UV, PHP/Composer, etc.).
- A publicly accessible URL via Cloudflare Tunnel.
- An entry in the local `sandboxes.db` registry.

## Actions

### Create a Sandbox
Create a new project container.
```bash
{baseDir}/scripts/create_sandbox.sh --stack <stack> --title <title>
```
Supported stacks: `nextjs`, `fastapi`, `laravel`, `rails`, `gin`, `springboot`, `dotnet`, `axum`, `ktor`, `vapor`, `flutter`, `phoenix`.

### List Sandboxes
View all active sandboxes and their URLs.
```bash
{baseDir}/scripts/list_sandboxes.sh
```

### Delete a Sandbox
Remove a container and its database entry (optionally keep volume).
```bash
{baseDir}/scripts/delete_sandbox.sh --name <container_name>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/essamamdani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
