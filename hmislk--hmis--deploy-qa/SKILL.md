---
name: deploy-qa
description: > Use when this capability is needed.
metadata:
  author: hmislk
---

# Deploy to QA Environment

Deploy the latest development code to QA1 or QA2 via GitHub Actions.

## Arguments

- `$0` - Target environment: `qa1` or `qa2`

## Pre-Deployment Checklist

Before deploying, MUST verify:

1. **persistence.xml uses environment variables** (run `/verify-persistence` first)
2. **No hardcoded DDL generation paths**
3. **All changes committed and pushed**

## Deployment Process

### QA1 Deployment
```bash
# Merge development into qa1 branch
git fetch origin
git checkout qa1
git merge origin/development
git push origin qa1
```

### QA2 Deployment
```bash
# Merge development into qa2 branch
git fetch origin
git checkout qa2
git merge origin/development
git push origin qa2
```

GitHub Actions will automatically:
1. Build the application with Maven
2. Deploy to the target QA server
3. Restart the Payara application server

## Post-Deployment

- Monitor GitHub Actions for build status
- Check QA environment is accessible after deployment
- Verify the deployed feature works as expected

## Troubleshooting

If deployment fails:
1. Check GitHub Actions logs for build errors
2. Verify persistence.xml configuration
3. Check if QA server is accessible
4. See [QA Troubleshooting Guide](../../developer_docs/deployment/qa-troubleshooting.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hmislk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
