---
name: opencode-supreme-setup
description: description: Environment configuration, variables, and secret management. Use when setting up environments. Use when this capability is needed.
metadata:
  author: skeletorflet
---
﻿---
name: env
description: Environment configuration, variables, and secret management. Use when setting up environments.
---

## env

Manage environment configuration.

### Best practices
- .env for local dev (never committed)
- .env.example for docs
- Validate at startup
- Secrets manager for production
- Different config per environment
- Never hardcode secrets

### Tools
- dotenv, doppler, vault
- GitHub Actions secrets
- Docker secrets
- Kubernetes secrets
- Cloud secret managers

---
> Source: [skeletorflet/opencode-supreme-setup](https://github.com/skeletorflet/opencode-supreme-setup) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
