---
name: ci-cd
description: Designs and reviews CI/CD pipelines. Covers build automation, test stages, deployment gates, caching, secrets management, and pipeline optimization. Use when setting up pipelines, debugging CI failures, or improving build times. Use when this capability is needed.
metadata:
  author: pvnarp
---

# CI/CD Pipeline

A pipeline has one job: prove the code is safe to ship, then ship it. Every step that doesn't serve that goal is waste.

## Pipeline Stages

```
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
│  Lint    │ → │  Build   │ → │  Test    │ → │  Deploy  │ → │  Verify  │
│          │   │          │   │          │   │ (staging)│   │          │
└──────────┘   └──────────┘   └──────────┘   └──────────┘   └──────────┘
                                                  │
                                            ┌─────┴─────┐
                                            │  Deploy   │
                                            │ (prod)    │
                                            └───────────┘
```

### 1. Lint & Format
- Run linter and formatter check (not fix - fail if code isn't formatted)
- Static analysis / type checking
- **Fast.** Should complete in under 30 seconds.

### 2. Build
- Compile / bundle production artifact
- Fail on warnings (treat warnings as errors in CI)
- Output build artifact for later stages (don't rebuild per stage)

### 3. Test
- Unit tests (fast, no external deps)
- Integration tests (database, APIs - may need service containers)
- E2E tests (if applicable - run in parallel where possible)
- **Test against the built artifact**, not a fresh build

### 4. Security Checks
- Dependency vulnerability scan (`npm audit`, `pip-audit`, etc.)
- Secret scanning (detect accidentally committed credentials)
- SAST (Static Application Security Testing) if available
- License compliance check (if applicable)

### 5. Deploy to Staging
- Automatic on main branch merge
- Run smoke tests against staging
- Gate: manual approval or automated health check before production

### 6. Deploy to Production
- Triggered by staging gate passing
- Canary or rolling deploy
- Automated rollback on health check failure

### 7. Post-Deploy Verification
- Smoke tests against production
- Monitor error rate for 15 minutes
- Alert if metrics degrade

## Pipeline Design Principles

### Speed
- **Target: < 10 min for PR checks, < 20 min for full deploy pipeline.**
- Parallelize independent stages (lint + test can run simultaneously)
- Cache dependencies aggressively (node_modules, pip cache, Docker layers)
- Only run what changed (monorepo: detect affected packages)
- Use matrix builds for multi-version testing, not sequential

### Caching
```yaml
# Cache pattern - adapt to your CI platform
cache:
  key: ${{ hashFiles('**/lock-file') }}
  paths:
    - node_modules/        # or .venv/, target/, etc.
    - ~/.cache/            # build tool caches
```

Cache by lock file hash. Invalidate when dependencies change, not on every commit.

### Secrets
- Never echo secrets in logs
- Use CI platform's secret storage (not env vars in config files)
- Rotate secrets on a schedule
- Scope secrets to the environments that need them (prod secrets not in PR builds)
- Mask secrets in output automatically

### Artifacts
- Build once, deploy the same artifact everywhere (dev → staging → prod)
- Tag artifacts with commit SHA or build number
- Retain artifacts for rollback (keep last N builds)

## PR Pipeline vs Main Pipeline

| Step | PR | Main |
|------|-----|------|
| Lint | Yes | Yes |
| Build | Yes | Yes |
| Unit tests | Yes | Yes |
| Integration tests | Yes | Yes |
| E2E tests | Optional (slow) | Yes |
| Security scan | Yes | Yes |
| Deploy staging | No | Yes |
| Deploy production | No | After gate |

## Common CI Platforms

### GitHub Actions
```yaml
on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4  # or python, go, etc.
      - run: npm ci
      - run: npm run lint
      - run: npm run build
      - run: npm test
```

### GitLab CI
```yaml
stages: [lint, build, test, deploy]

lint:
  stage: lint
  script: npm run lint

test:
  stage: test
  script: npm test
  services:
    - postgres:16  # service containers for integration tests
```

## Debugging CI Failures

1. **Reproducing locally**: Run the exact same commands CI runs. If it passes locally but fails in CI, the difference is the environment.
2. **Flaky tests**: If a test fails intermittently in CI, it's flaky. Fix or delete it. Don't add retries.
3. **Cache corruption**: If builds fail mysteriously after a dependency change, clear the cache.
4. **Resource limits**: CI runners have limited CPU/memory. Tests that pass locally may OOM in CI.
5. **Timing issues**: Tests depending on wall clock time or sleeps are fragile in CI where resources are shared.

## Pipeline Review Checklist

- [ ] Every stage has a clear purpose (no "just in case" steps)
- [ ] Pipeline completes in < 10 min for PRs
- [ ] Dependencies cached effectively
- [ ] Secrets not exposed in logs or config
- [ ] Failure messages are actionable (not just "exit code 1")
- [ ] Pipeline works for new contributors (no hidden setup steps)
- [ ] Deploy pipeline has rollback capability
- [ ] Status checks required for PR merge

---
> Source: [pvnarp/agent-skills](https://github.com/pvnarp/agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
