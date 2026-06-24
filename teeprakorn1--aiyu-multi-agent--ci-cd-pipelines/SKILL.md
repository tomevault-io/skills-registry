---
name: ci-cd-pipelines
description: Continuous Integration and Continuous Deployment best practices and pipeline design Use when this capability is needed.
metadata:
  author: teeprakorn1
---

# CI/CD Pipelines - Continuous Integration & Deployment

> Automate everything. Fast feedback, reliable releases.

---

## CI/CD Principles

### The Pipeline Stages

```
Code Commit → Build → Test → Security → Deploy → Monitor
     │          │      │        │         │        │
     ▼          ▼      ▼        ▼         ▼        ▼
  Linting   Compile  Unit    Scan    Staging   Health
  Formatting Package  Integration  Audit   Prod      Metrics
```

### Core Principles

| Principle | Description |
|-----------|-------------|
| **Fast Feedback** | Fail fast, within 10 minutes |
| **Repeatable** | Same input = same output |
| **Isolated** | No dependencies on other pipelines |
| **Versioned** | Pipeline as code, version controlled |

---

## Pipeline Design

### GitHub Actions Example

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Lint
        run: npm run lint
      
      - name: Test
        run: npm test
        
      - name: Build
        run: npm run build
      
      - name: Security audit
        run: npm audit --audit-level=moderate

  deploy:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to production
        run: |
          echo "Deploying to production..."
          # Deploy commands here
```

### Pipeline Optimization

| Strategy | Implementation |
|----------|----------------|
| **Parallel Jobs** | Run lint, test, build concurrently |
| **Caching** | Cache dependencies between runs |
| **Conditional Steps** | Skip deploy on PRs |
| **Artifacts** | Pass build artifacts between jobs |

---

## Deployment Strategies

### Blue-Green Deployment

```
Blue (Current)     Green (New)
┌─────────┐       ┌─────────┐
│  v1.2.3 │◄──────│  v1.3.0 │
└────┬────┘       └────┬────┘
     │                 │
     └────────┬────────┘
              ▼
           Router
              │
              ▼
           Users

Switch traffic instantly
Rollback by switching back
```

### Canary Deployment

```
Progressive rollout:

100% v1.2.3 → 95% v1.2.3 + 5% v1.3.0
            → 80% v1.2.3 + 20% v1.3.0
            → 50% v1.2.3 + 50% v1.3.0
            → 0% v1.2.3 + 100% v1.3.0
```

**Decision criteria:**
- Error rate < 0.1%
- Response time p95 < 200ms
- No critical alerts

---

## Quality Gates

### Pre-Deployment Checks

```yaml
quality_gates:
  test_coverage:
    min_percentage: 80
    
  security_scan:
    fail_on: [critical, high]
    
  performance:
    max_response_time: 200ms
    
  code_quality:
    max_complexity: 15
    lint_errors: 0
```

### Automated Rollback Triggers

| Metric | Threshold | Action |
|--------|-----------|--------|
| Error Rate | > 1% | Alert + Consider rollback |
| Response Time | > 500ms p95 | Alert |
| CPU Usage | > 80% for 5min | Scale up |
| Memory Usage | > 90% | Alert + Investigate |

---

## Environment Management

### Environment Promotion

```
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│  Local  │───►│   Dev   │───►│  Staging│───►│   Prod  │
└─────────┘    └─────────┘    └─────────┘    └─────────┘
   Developer      CI/CD         Pre-prod       Production
   testing       deploys        validation     release
```

### Environment Variables

```bash
# Environment-specific configs
# NEVER commit sensitive values!

# .env.development
API_URL=http://localhost:3000
DEBUG=true

# .env.production
API_URL=https://api.example.com
DEBUG=false
LOG_LEVEL=warn
```

---

## Verification Checklist

- [ ] Pipeline runs on every commit
- [ ] Tests run in < 10 minutes
- [ ] Security scans integrated
- [ ] Deployment requires approval for production
- [ ] Rollback procedure documented
- [ ] Pipeline code is version controlled
- [ ] Notifications configured for failures
- [ ] Artifacts cached between stages

---

> 🔄 **Remember:** If it hurts, do it more often. CI/CD makes releases boring.

---
> Source: [teeprakorn1/aiyu-multi-agent](https://github.com/teeprakorn1/aiyu-multi-agent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
