---
name: deploy
description: Deploy the Astro static site to Cloudflare Pages Use when this capability is needed.
metadata:
  author: khaym
---

# Deploy Skill

Deploy the Astro static build output to Cloudflare Pages. Includes WSL authentication workaround.

## Prerequisites

- Cloudflare account exists
- `astro.config.mjs` is configured for static output (no adapter)
- Wrangler CLI is installed (`npm install -D wrangler`)

## Deployment Target: Workers vs Pages

| | Workers | Pages |
|---|---|---|
| Use case | SSR, API, dynamic processing | Static sites, JAMstack |
| Deploy command | `wrangler deploy` | `wrangler pages deploy` |
| Git auto-deploy | Build CI yourself | GitHub integration from dashboard |
| Preview URLs | None | Auto-generated per PR |

Use Pages for static sites. If SSR is needed later, add `adapter: cloudflare()` to `astro.config.mjs` to switch to Workers.

## Steps

### Step 1: Verify Credentials

Cloudflare credentials should already be set as shell environment variables during `/setup`.
If not, set them now (do **not** create a `.env` file):

```bash
export CLOUDFLARE_API_TOKEN=<token-value>
export CLOUDFLARE_ACCOUNT_ID=<account-id>
```

See `/setup` skill (Phase 1, Step 1-3) for how to obtain these values.

> **Important:** Omitting `CLOUDFLARE_ACCOUNT_ID` may cause authentication errors (`/memberships` endpoint, code: 10001). Always set it.

### Step 2: Create Pages Project (First Time Only)

```bash
npx wrangler pages project create <project-name> --production-branch main
```

On success, a URL like `<project-name>-xxx.pages.dev` is assigned.

### Step 3: Build

```bash
npm run build
```

Static files are output to `dist/`.

### Step 4: Deploy

```bash
npx wrangler pages deploy ./dist --project-name <project-name> --branch main
```

On success, the following are displayed:
- Preview URL: `https://<hash>.<project-name>-xxx.pages.dev`
- Production URL: `https://<project-name>-xxx.pages.dev`

### Subsequent Deployments

Only Step 3 (build) and Step 4 (deploy) are needed. Only changed files are uploaded.

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `wrangler login` errors after browser auth | WSL localhost callback unreachable | Use API token + environment variables |
| `Unable to authenticate request [code: 10001]` | `CLOUDFLARE_ACCOUNT_ID` not set | Set Account ID in environment variable |
| `Invalid API Token` | Copy error or insufficient permissions | Recreate token, verify Pages Edit permission |
| Favicon not updating | Browser cache | Ctrl+Shift+R to force reload |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/khaym) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
