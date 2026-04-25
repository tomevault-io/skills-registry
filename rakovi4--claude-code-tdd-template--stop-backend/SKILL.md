---
name: stop-backend
description: Stop the running Spring Boot backend application. Use when user wants to stop the backend server or mentions /stop-backend command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# Stop Backend Application

## Action

Use `TaskStop` with the backend task ID (from `/run-backend` output).

If task ID unknown, kill by port:
```bash
for /f "tokens=5" %a in ('netstat -ano ^| findstr :8080 ^| findstr LISTENING') do taskkill /F /PID %a
```

## Output

Report: backend stopped.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
