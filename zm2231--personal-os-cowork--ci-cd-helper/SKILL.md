---
name: ci-cd-helper
description: CI/CD pipeline optimization, build failures, and deployment automation for DevOps workflows. Use when this capability is needed.
metadata:
  author: zm2231
---

# CI/CD Helper

CI/CD pipeline optimization, build failures, and deployment automation strategies.

## Tier 1: Guided CI/CD

### What This Skill Does

Guides you through setting up and optimizing CI/CD pipelines for automated testing and deployment.

### When to Use This Skill

- **Pipeline Setup**: Creating CI/CD workflows
- **Build Failures**: Debugging and fixing failed builds
- **Pipeline Optimization**: Improving pipeline speed and efficiency
- **Deployment Automation**: Automating release processes
- **Monitoring**: Tracking pipeline health and metrics

### Guided CI/CD Workflow

#### Step 1: Pipeline Design

**Pipeline Stages:**
```
1. Checkout Code
2. Install Dependencies
3. Lint Code
4. Run Tests
5. Build Artifact
6. Security Scan
7. Deploy to Staging
8. Run Integration Tests
9. Deploy to Production
10. Verify Deployment
```

#### Step 2: Setup Pipeline

**GitHub Actions Example:**
```yaml
name: CI/CD Pipeline

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.9'
      - name: Install dependencies
        run: pip install -r requirements.txt
      - name: Run tests
        run: pytest

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Build Docker image
        run: docker build -t myapp .

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to production
        run: kubectl apply -f k8s/
```

#### Step 3: Debug Build Failures

**Common Failure Patterns:**
- Dependency conflicts
- Test failures
- Lint errors
- Build timeout
- Permission issues
- Resource exhaustion

**Debugging Steps:**
```bash
# Run pipeline locally
docker-compose run ci

# Check logs
kubectl logs <pod-name>

# SSH into build environment
docker exec -it <container-id> bash
```

#### Step 4: Optimize Pipeline

**Optimization Strategies:**
- Cache dependencies
- Parallelize jobs
- Use matrix builds efficiently
- Minimize build context
- Use layer caching (Docker)
- Run tests selectively

---

## Tier 2: Confident CI/CD

### Advanced Techniques

#### 1: Caching Strategies

**Dependency Caching:**
```yaml
- name: Cache pip packages
  uses: actions/cache@v2
  with:
    path: ~/.cache/pip
    key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}
```

#### 2: Parallel Execution

**Matrix Builds:**
```yaml
strategy:
  matrix:
    os: [ubuntu-latest, macos-latest, windows-latest]
    python-version: [3.8, 3.9, '3.10']
```

#### 3: Deployment Strategies

**Blue-Green Deployment:**
```yaml
- name: Deploy to green
  run: kubectl apply -f k8s/green-deployment.yaml

- name: Verify green
  run: ./health-check.sh https://green.example.com

- name: Switch traffic
  run: kubectl patch service myapp -p '{"spec":{"selector":{"version":"green"}}}'
```

---

## Tier 3: Anticipatory CI/CD

### Proactive Strategies

#### 1: Predictive Failure Detection

**Analyze Past Failures:**
```python
def predict_build_failure(repo):
    """Predict likelihood of build failure"""
    recent_changes = get_recent_commits(repo)
    test_history = get_test_history(repo)
    complexity_metrics = calculate_complexity(repo)

    risk_score = calculate_risk(recent_changes, test_history, complexity_metrics)

    return risk_score
```

#### 2: Pipeline Health Monitoring

**Metrics Dashboard:**
- Build success rate
- Average build time
- Deployment frequency
- Failure recovery time
- Pipeline cost

### Best Practices

**Do:**
- Use feature branches
- Test locally before push
- Cache dependencies
- Parallelize jobs
- Monitor pipeline health
- Rollback automatically on failure

**Don't:**
- Deploy untested code
- Ignore build failures
- Skip security scans
- Deploy to production manually
- Use secrets in logs
- Ignore pipeline performance

---

## Related Skills

- **debugging**: Debugging build failures
- **code-review**: Reviewing CI/CD changes
- **workflow-orchestrator**: Automating pipelines

---
> Source: [zm2231/personal-os-cowork](https://github.com/zm2231/personal-os-cowork) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
