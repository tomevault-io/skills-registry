---
name: deployment
description: Build and deploy the Next.js application to GitHub Pages. Use when this capability is needed.
metadata:
  author: jalexisg
---

# Deployment Skill

This skill encapsulates the knowledge required to build and deploy the application.

## Commands

- **Build**: `npm run build`
  - Generates static files in the `out/` directory.
  - Requires `output: 'export'` in `next.config.ts`.
- **Deploy**: `git push origin main`
  - Triggers GitHub Actions workflow `.github/workflows/deploy.yml`.

## Configuration
- **GitHub Actions**: [.github/workflows/deploy.yml](../../.github/workflows/deploy.yml)
- **Next.js Config**: [next.config.ts](../../next.config.ts)

## Docker Controls

- **Start**: `docker-compose up -d`
- **Stop**: `docker-compose down`
- **Rebuild**: `docker-compose up --build -d`
- **Logs**: `docker-compose logs -f`

## Troubleshooting
If port 3000 is in use:
1. Check running containers: `docker ps`
2. Stop old containers: `docker-compose down`
3. Check system processes: `lsof -i :3000`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jalexisg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
