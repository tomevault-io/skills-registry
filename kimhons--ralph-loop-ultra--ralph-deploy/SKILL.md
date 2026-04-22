---
name: ralph-deploy
description: Check deployment readiness and deploy to configured platforms (Vercel, Render, AWS, Azure, Supabase). Runs cloud-deployer skill for pre-flight checks, then executes deployment. Use when ready to ship. Use when this capability is needed.
metadata:
  author: kimhons
---

## Ralph Ultra Deployment

Check deployment readiness and deploy to configured platforms.

### What this does

1. **Pre-flight** — Runs cloud-deployer skill for readiness check
2. **Verification** — Runs full 5-gate verification pipeline
3. **Build** — Executes production build
4. **Deploy** — Pushes to configured platform(s)
5. **Post-deploy** — Health check on deployed URL

### Usage

```
/ralph-ultra:ralph-deploy [--platform vercel|render|aws|azure] [--skip-verify] [--dry-run]
```

### Supported Platforms

| Platform | CLI Required | Config File |
|----------|-------------|-------------|
| Vercel | `vercel` | vercel.json |
| Render | `render` | render.yaml |
| AWS | `aws` | samconfig.toml, cdk.json |
| Azure | `az` | azure-pipelines.yml |
| Supabase | `supabase` | supabase/config.toml |

### Safety

- Always runs in dry-run mode first unless `--skip-verify` is set
- Requires explicit confirmation before production deployments
- Checks environment variables are set before deploying
- Verifies build output exists and is valid

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimhons) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
