---
name: docker-ops
description: > Use when this capability is needed.
metadata:
  author: dboone323
---

# Docker Operations Expert

Specialized knowledge for the Docker infrastructure in this project.

## Services (docker-compose.yml)

| Service       | Port  | Purpose               |
| ------------- | ----- | --------------------- |
| Ollama        | 11434 | Local LLM server      |
| Open WebUI    | 3001  | Chat interface        |
| Redis         | 6379  | Caching & task queue  |
| Redis Insight | 5540  | Redis management UI   |
| Grafana       | 3000  | Monitoring dashboards |
| Prometheus    | 9090  | Metrics collection    |
| Dozzle        | 8888  | Container log viewer  |

## Common Operations

### Start/Stop

```bash
docker compose up -d          # Start all
docker compose down           # Stop all
docker compose restart redis  # Restart specific
```

### Health Check

```bash
docker compose ps
docker compose logs -f ollama
curl -sI localhost:5005/status  # MCP server
```

### Cleanup

```bash
docker system prune -f        # Remove unused
docker volume prune -f        # Remove volumes
docker image prune -a -f      # Remove images
```

### Troubleshooting

```bash
# Check container logs
docker logs tools-automation-redis-1

# Enter container shell
docker exec -it tools-automation-ollama-1 sh

# Check resource usage
docker stats
```

## Network

All services on `tools_network` bridge network.
Internal DNS uses service names (e.g., `redis:6379`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dboone323) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
