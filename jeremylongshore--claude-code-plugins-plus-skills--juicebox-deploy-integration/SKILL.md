---
name: juicebox-deploy-integration
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox Deploy Integration

## Docker
```dockerfile
FROM node:20-slim
WORKDIR /app
COPY package*.json ./
RUN npm ci --production
COPY dist/ ./dist/
ENV JUICEBOX_API_KEY=""
CMD ["node", "dist/index.js"]
```

## Cloud Run
```bash
echo -n "jb_live_..." | gcloud secrets create juicebox-key --data-file=-
gcloud run deploy recruiter-svc --set-secrets JUICEBOX_API_KEY=juicebox-key:latest
```

## Resources
- [Juicebox API](https://docs.juicebox.work)

## Next Steps
See `juicebox-webhooks-events`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
