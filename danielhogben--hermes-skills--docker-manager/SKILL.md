---
name: docker-manager
description: Manage Docker containers across the home lab fleet. List, start, stop, Use when this capability is needed.
metadata:
  author: Danielhogben
---

# Docker Manager

Manage Docker containers across the home lab fleet.

## Docker Hosts

| Host | Docker | Notes |
|------|--------|-------|
| server | Yes | Main container host (no active containers currently) |
| workstation | Yes | Available but rarely used |

## Commands

### Container status

```bash
# All containers
python3 docker_manager.py status

# Running only
python3 docker_manager.py status --running

# On specific host
python3 docker_manager.py status --host server
```

### Container operations

```bash
python3 docker_manager.py start <container>
python3 docker_manager.py stop <container>
python3 docker_manager.py restart <container>
python3 docker_manager.py logs <container> [--lines 50]
```

### Image management

```bash
python3 docker_manager.py images
python3 docker_manager.py pull <image>
python3 docker_manager.py rmi <image>
```

### Cleanup

```bash
# Remove stopped containers, unused images, build cache
python3 docker_manager.py prune

# Aggressive cleanup (including volumes)
python3 docker_manager.py prune --volumes
```

### System info

```bash
python3 docker_manager.py info
python3 docker_manager.py disk
```

## Workflow

### "Container won't start"

1. Check status: `python3 docker_manager.py status --host server`
2. Check logs: `python3 docker_manager.py logs <container> --lines 100`
3. Check disk: `python3 docker_manager.py disk`
4. Check image: `python3 docker_manager.py images`
5. Restart: `python3 docker_manager.py restart <container>`

### "Disk is full"

1. Check Docker disk usage: `python3 docker_manager.py disk`
2. Prune: `python3 docker_manager.py prune`
3. Remove unused images: `python3 docker_manager.py images` then `rmi` old ones
4. Check volumes: `docker system df -v`

### Docker Compose

```bash
# If using docker-compose files
ssh server 'cd /opt/<project> && docker compose ps'
ssh server 'cd /opt/<project> && docker compose up -d'
ssh server 'cd /opt/<project> && docker compose logs --tail 50'
```

## Security

- Docker socket access = root — treat as sensitive
- Don't run untrusted images
- Use `--read-only` and `--security-opt no-new-privileges` for production containers
- Keep images updated for security patches
- Monitor container resource limits to prevent host exhaustion

---
> Source: [Danielhogben/hermes-skills](https://github.com/Danielhogben/hermes-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
