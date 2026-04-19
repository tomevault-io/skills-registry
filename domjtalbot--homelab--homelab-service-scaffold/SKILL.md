---
name: homelab-service-scaffold
description: Create or add a new service in the homelab repo when the user asks to 'create a new service' or 'add a new service'. Enforce repo conventions for docker compose, env examples, README docs, Traefik/Homepage labels, healthchecks, optional hooks, and optional cron jobs. Use when this capability is needed.
metadata:
  author: domjtalbot
---

# Homelab Service Scaffold

Follow this workflow whenever adding a new service.

## Core Rules (must follow)

- **Required files**: `services/<name>/docker-compose.yml`, `services/<name>/README.md`, `services/<name>/.env.<name>.example`.
- **Optional**: `services/<name>/Taskfile.yml` **only** if hooks are needed (ask if unsure).
- **Cron tasks**: If scheduled jobs are needed, propose the Ofelia pattern (labels + scripts + status JSON). If not needed, do not add cron plumbing.
- **Compose envs**: `docker-compose.yml` **must use env_file** and **must not** include an `environment:` block.
- **Labels**: Always add **Traefik** + **Homepage** labels.
- **Healthcheck**: Always define a container healthcheck.
- **No root edits**: Do **not** edit root `.env`, `.env.example`, root `Taskfile.yml`, or other services (Homepage config is the only exception, and only if labels cannot be used).
- **Secrets**: Never commit API keys, tokens, domains, or absolute host paths outside the repo. Use placeholders and comments in `.env.<name>.example`.
- **Defaults**: Use image defaults for port mappings and comment out optional envs.
- **Mode**: Use **production mode** if the image supports it.
- **Docs**: Use official docs/GitHub repo; browse when necessary.
- **Validation**: Run `task up SERVICE=<name>` and debug until healthy, Traefik routes, and Homepage entry work.

## Workflow

1. **Identify the service**
   - Confirm service name, image, ports, and subdomain.
   - Ask about persistence needs, storage paths, auth/secrets, and whether a health endpoint exists.
   - If the service likely needs cron jobs or hooks, suggest them and ask for confirmation.

2. **Compose file** (`services/<name>/docker-compose.yml`)
   - Use `env_file` for config.
   - Map ports using env vars with defaults matching the image.
   - Add Traefik + Homepage labels.
   - Add a healthcheck (HTTP or command-based).
   - Add networks/volumes as needed; prefer external `traefik` network.

3. **Env example** (`services/<name>/.env.<name>.example`)
   - Document all vars and comment optional/default values.
   - Never include secrets or user-specific paths.
   - Use placeholders for secrets (e.g., `changeme_...`).

4. **README** (`services/<name>/README.md`)
   - Explain what the service does, how it’s wired in this homelab, URLs, and healthcheck.
   - List required/optional env vars and volumes.
   - Describe any init steps.

5. **Hooks (optional)**
   - Add `Taskfile.yml` only if a hook is needed (e.g., create network, pre-up steps). Ask first if unsure.

6. **Cron (optional)**
   - If needed, use the existing Ofelia pattern:
     - Job labels on the target service
     - Script(s) under `services/<name>/scripts/`
     - Status JSON served by ofelia-status
     - Homepage entries only if labels are insufficient

7. **Bring it up and verify**
   - Run `task up SERVICE=<name>`.
   - Verify `docker ps` health, Traefik route, Homepage entry.
   - Debug until green.

## Before commit checklist

- No secrets or personal paths in tracked files.
- Healthcheck works.
- Traefik route works.
- Homepage entry appears.
- README updated and accurate.

## Reference

See `references/service-checklist.md` for a detailed checklist.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/domjtalbot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
