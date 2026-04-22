---
name: deploy
description: Deploy the application or manage infrastructure. Handles Docker builds, CI/CD, and deployment workflows. Use when this capability is needed.
metadata:
  author: yu-iskw
---

# Deploy

Execute the following deployment/infrastructure task:

$ARGUMENTS

## Pre-deployment Checklist

1. **Verify build**: `pnpm build`
2. **Run tests**: `pnpm test`
3. **Check lint**: `pnpm lint`
4. **Review environment**: Verify .env configuration

## Docker Deployment

```bash
# Build and start all services
docker-compose up --build

# Build specific service
docker-compose build [service-name]

# View logs
docker-compose logs -f [service-name]
```

## Environment Variables

Required variables (see .template.env):

- `GOOGLE_CLOUD_PROJECT` / `GOOGLE_API_KEY` — AI provider credentials
- `LANGSMITH_API_KEY` — Observability (optional)
- `PORT` — Backend server port (default: 3000)

## Post-deployment Verification

1. Health check: `curl http://localhost:3000/health`
2. Agent types: `curl http://localhost:3000/chat/agent-types`
3. Test chat: Send a test message through the UI or API

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yu-iskw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
