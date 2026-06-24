---
name: ci-cd-automation
description: > Use when this capability is needed.
metadata:
  author: khoabd
---

# CI/CD & Automation

Every commit should be deployable. Automate everything that runs more than twice.

## Pipeline Stages

```
Commit → Lint → Test → Build → Security Scan → Deploy (staging) → Smoke Test → Deploy (prod)
```

## GitHub Actions Template

```yaml
on: [push, pull_request]
jobs:
  ci:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Lint
        run: npm run lint
      - name: Test
        run: npm test
      - name: Build
        run: npm run build
      - name: Security scan
        run: npx audit-ci --moderate
```

## Deployment Strategy
- **Blue/green** — zero-downtime, instant rollback
- **Canary** — 5% → 25% → 100% with metric gates
- **Feature flags** — decouple deploy from release

## Red Flags
- Manual deployment steps
- No rollback plan
- Tests skipped to speed up pipeline
- Secrets in pipeline logs
- No smoke test after deploy

---
> Source: [khoabd/paperclip](https://github.com/khoabd/paperclip) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
