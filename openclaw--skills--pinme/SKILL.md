---
name: pinme
description: | Use when this capability is needed.
metadata:
  author: openclaw
---

# PinMe - Zero-Config Frontend Deployment

Deploy static websites to IPFS network with a single command. No servers, no accounts, no setup.

## When to Use

Use this skill when:
- User asks to "deploy", "upload", or "publish" a frontend project
- User wants to get a preview URL for their built site
- User mentions PinMe or IPFS deployment
- Project has built static files (dist/, build/, out/, public/)

## Quick Start

```bash
# Install PinMe
npm install -g pinme

# Deploy (auto-detects static directory)
pinme upload dist

# Get preview URL
# https://pinme.eth.limo/#/preview/*
```

## Core Workflow

### 1. Check Prerequisites
```bash
# Check Node.js version (requires 16.13.0+)
node --version

# Verify pinme is installed
pinme --version
```

### 2. Identify Static Directory
PinMe auto-detects directories in priority order:

| Directory | Framework/Tool |
|-----------|---------------|
| `dist/` | Vite, Vue CLI, Angular |
| `build/` | Create React App |
| `out/` | Next.js (static export) |
| `public/` | Static sites |

**Validation rules:**
- ✅ Folder must exist
- ✅ Must contain `index.html`
- ✅ Must have actual static files (CSS, JS, images)

### 3. Execute Deployment
```bash
# Deploy dist directory (most common)
pinme upload dist

# Deploy specific directory
pinme upload build

# Upload and bind to custom domain (requires Plus)
pinme upload dist --domain my-site
```

### 4. Return Result
Return ONLY the preview URL: `https://pinme.eth.limo/#/preview/*`

## Commands Reference

| Command | Description |
|---------|-------------|
| `pinme upload <dir>` | Upload static files to IPFS |
| `pinme upload <dir> --domain <name>` | Upload + bind domain |
| `pinme import <car-file>` | Import CAR files |
| `pinme export <CID>` | Export IPFS content as CAR |
| `pinme list` | Show upload history |
| `pinme rm <hash>` | Remove files from IPFS |
| `pinme set-appkey` | Set AppKey for authentication |
| `pinme my-domains` | List owned domains |
| `pinme --version` | Show version |

## Upload Limits

| Type | Free Plan |
|------|----------|
| Single file | 200MB |
| Total directory | 1GB |

## Error Handling

| Error | Solution |
|-------|----------|
| Node.js version too low | Upgrade to 16.13.0+ |
| Command not found | Run `npm install -g pinme` |
| Folder does not exist | Check path, use `ls` |
| Upload failed | Check network, retry |
| Authentication failed | Run `pinme set-appkey` |

## AI Execution Protocol

For programmatic deployment:

1. **Check environment**: `node --version` (needs >=16.13.0)
2. **Install if needed**: `npm install -g pinme`
3. **Detect directory**: Check `dist/`, `build/`, `out/`, `public/`
4. **Validate**: Must contain `index.html`
5. **Execute**: `pinme upload <directory>`
6. **Return**: Only the preview URL

**Prohibited operations:**
- ❌ Upload node_modules, .env, .git
- ❌ Upload source directories (src/)
- ❌ Upload config files (package.json, etc.)
- ❌ Upload non-existent/empty folders

## GitHub Actions Integration

Example workflow:

```yaml
name: Deploy to PinMe
on: [push]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
      - run: npm ci && npm run build
      - run: npm install -g pinme
      - run: pinme set-appkey "${{ secrets.PINME_APPKEY }}"
      - run: pinme upload dist --domain "${{ secrets.DOMAIN }}"
```

## Machine-Readable Config

```json
{
  "tool": "pinme",
  "requirements": {
    "node_version": ">=16.13.0"
  },
  "install": "npm install -g pinme",
  "upload": "pinme upload {{directory}}",
  "upload_with_domain": "pinme upload {{directory}} --domain {{domain}}",
  "validDirectories": ["dist", "build", "out", "public"],
  "requiredFiles": ["index.html"],
  "excludePatterns": ["node_modules", ".env", ".git", "src"],
  "limits": {
    "single_file": "200MB",
    "total_directory": "1GB"
  },
  "output": "preview_url",
  "preview_url_format": "https://pinme.eth.limo/#/preview/*",
  "fixed_domain_format": "https://*.pinit.eth.limo"
}
```

## Resources

- **Website:** https://pinme.eth.limo/
- **GitHub:** https://github.com/glitternetwork/pinme
- **AppKey:** https://pinme.eth.limo/ (get from dashboard)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
