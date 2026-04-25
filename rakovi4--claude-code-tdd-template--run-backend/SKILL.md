---
name: run-backend
description: Run the Spring Boot backend application. Use when user wants to start the backend server or mentions /run-backend command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# Run Backend Application

## Action

Run in background (default profile):
```bash
cd backend && ./gradlew.bat :application:bootRun
```

With argument (profile name):
```bash
cd backend && ./gradlew.bat :application:bootRun --args="--spring.profiles.active={argument}"
```

Use `run_in_background: true` so the server keeps running.

## Output

Report startup status. Server runs at http://localhost:8080

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
