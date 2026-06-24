---
name: logs
description: View and aggregate logs from various sources (systemd, docker, application files). Use when debugging, monitoring services, or investigating errors. Use when this capability is needed.
metadata:
  author: allanninal
---

# Log Aggregator

View and search logs from multiple sources across your projects.

## Arguments
- `$0`: Service name or project (optional - shows all if not specified)
- `$1`: Options - `error`, `warn`, `tail`, `search:pattern` (optional)

## Log Sources by Project

| Project | Source | Location/Command |
|---------|--------|------------------|
| eruditiontx-services-mvp | systemd | `journalctl -u erudition-service` |
| eruditiontx-services-mvp | app | `~/Projects/eruditiontx-services-mvp/logs/` |
| mathmatterstx-services | systemd | `journalctl -u mathmatters-service` |
| agila-tax-management | docker | `docker logs agila-backend` |
| notaryo.ph | Next.js | `.next/` logs, terminal output |
| bocs-turbo | Vercel | `vercel logs` |

## Commands

### View Recent Logs

**Systemd Services:**
```bash
journalctl -u $SERVICE -n 100 --no-pager
# Follow mode
journalctl -u $SERVICE -f
```

**Docker Containers:**
```bash
docker logs $CONTAINER --tail 100
# Follow mode
docker logs $CONTAINER -f
```

**Application Log Files:**
```bash
tail -n 100 ~/Projects/$PROJECT/logs/app.log
# Follow mode
tail -f ~/Projects/$PROJECT/logs/app.log
```

### Filter by Level

**Errors Only:**
```bash
# Systemd
journalctl -u $SERVICE -p err -n 100 --no-pager

# Docker/Files
docker logs $CONTAINER 2>&1 | grep -i "error\|exception\|traceback"

# Log files
grep -i "error\|exception\|traceback" ~/Projects/$PROJECT/logs/app.log
```

**Warnings:**
```bash
grep -i "warn\|warning" $LOG_SOURCE
```

### Search Logs

```bash
# Search for pattern
journalctl -u $SERVICE | grep -i "$PATTERN"
docker logs $CONTAINER 2>&1 | grep -i "$PATTERN"
grep -i "$PATTERN" ~/Projects/$PROJECT/logs/*.log
```

### Time-Based Filtering

```bash
# Last hour
journalctl -u $SERVICE --since "1 hour ago"

# Today
journalctl -u $SERVICE --since today

# Specific time range
journalctl -u $SERVICE --since "2024-01-01 00:00:00" --until "2024-01-01 23:59:59"
```

## Log Aggregation Mode

View logs from multiple services at once:

```bash
# All Erudition services
journalctl -u erudition-service -u mathmatters-service -f

# All Docker containers
docker-compose logs -f
```

## Log Analysis

### Count Errors by Type
```bash
grep -i "error" $LOG_FILE | sort | uniq -c | sort -rn | head -20
```

### Find Slow Requests
```bash
grep -E "took [0-9]+ms" $LOG_FILE | awk '{print $NF}' | sort -rn | head -10
```

### Track Request IDs
```bash
grep "$REQUEST_ID" $LOG_FILE
```

## Output Format

```
Logs: [service-name]
Source: [systemd/docker/file]
Filter: [all/error/warn]
Time: [range]

---
[Formatted log entries with timestamps]
---

Summary:
- Total entries: X
- Errors: Y
- Warnings: Z
- Time span: [start] to [end]
```

## Common Log Patterns

### FastAPI (Python)
```
INFO:     127.0.0.1:52847 - "GET /health HTTP/1.1" 200 OK
ERROR:    Exception in route handler: [error message]
```

### Next.js
```
ready - started server on 0.0.0.0:3000
error - Error: [error message]
warn - [warning message]
```

### Docker
```
[timestamp] [level] [message]
```

## Troubleshooting

If logs are empty:
1. Check if service is running: `systemctl status $SERVICE`
2. Check log rotation: `ls -la /var/log/`
3. Check Docker container status: `docker ps -a`
4. Verify log file permissions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
