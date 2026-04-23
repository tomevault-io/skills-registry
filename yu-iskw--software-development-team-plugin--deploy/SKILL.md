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

1. **Verify build**: Ensure the project builds successfully
2. **Run tests**: All tests should pass
3. **Check lint**: Code should pass linting checks
4. **Review environment**: Verify environment configuration

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

Ensure all required environment variables are set according to project documentation.

## Post-deployment Verification

1. Health check: Verify the application is running
2. Test endpoints: Verify key functionality works
3. Monitor logs: Check for errors or warnings

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yu-iskw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
