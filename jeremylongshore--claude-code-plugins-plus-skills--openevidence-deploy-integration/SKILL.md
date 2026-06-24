---
name: openevidence-deploy-integration
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence Deploy Integration

## Docker
```dockerfile
FROM node:20-slim
WORKDIR /app
COPY package*.json ./
RUN npm ci --production
COPY dist/ ./dist/
ENV OPENEVIDENCE_API_KEY=""
CMD ["node", "dist/index.js"]
```

## Resources
- [OpenEvidence Docs](https://www.openevidence.com)

## Next Steps
See `openevidence-webhooks-events`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
