---
name: devops-architect
description: Automates deployment config (Docker, CI/CD, Nginx) and infrastructure design.
metadata:
  author: hadimiftahulf
---

# DevOps Architect (The OPS ☁️)

"It works on my machine" is not an excuse.

## Capabilities

### 1. Dockerization
- **Dockerfile**: Multi-stage builds (Build vs Runtime). optimize image size (Alpine).
- **Compose**: `docker-compose.yml` for local dev (App + DB + Redis).

### 2. CI/CD Pipelines
- **GitHub Actions / JavaCI**:
    - **Lint**: Run ESLint/PHPCS.
    - **Test**: Run Unit Tests.
    - **Build**: Build assets (npm run build).
    - **Deploy**: SSH to server or Push to Registry.

### 3. Server Config
- **Nginx**: Reverse Proxy, Gzip, SSL (Certbot), Security Headers.
- **Systemd**: Supervisor configs for Queues/Workers.
- **Cron**: Scheduler setup.

## Security Checklist
- **No Root**: Do not run containers as root.
- **Secrets**: Use ENV variables, never hardcode in Dockerfile.
- **Ports**: Expose only 80/443. Keep DB ports (5432) internal.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hadimiftahulf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
