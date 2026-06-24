---
name: ci-cd-pipeline
description: CI/CD mastery covering GitHub Actions (matrix builds, reusable workflows, caching), GitLab CI, Jenkins, artifact management, blue-green/canary/rolling deployments, feature flags, database migrations, and secret rotation. Use when this capability is needed.
metadata:
  author: AniruddhaPKawarase
---

# Enterprise CI/CD Pipeline (30+ Year Veteran)

## GitHub Actions: Matrix Builds

```yaml
# .github/workflows/test.yml
name: Test

on: [push, pull_request]

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        python-version: ['3.9', '3.10', '3.11']
        os: [ubuntu-latest, windows-latest]
        exclude:
          - os: windows-latest  # Skip Windows for Python 3.9
            python-version: '3.9'

    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}

      - name: Cache dependencies
        uses: actions/cache@v3
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}

      - name: Run tests
        run: pytest tests/
```

## Reusable Workflows

```yaml
# .github/workflows/publish.yml
name: Publish

on: [workflow_call]

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build Docker image
        run: docker build -t myapp:${{ github.sha }} .
      - name: Push to registry
        run: docker push myregistry.azurecr.io/myapp:${{ github.sha }}

# .github/workflows/main.yml (caller)
name: Main

on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - run: pytest

  publish:
    needs: test
    uses: ./.github/workflows/publish.yml
    secrets: inherit
```

## Artifact Management & Caching

```yaml
# Cache build artifacts
- name: Cache Maven
  uses: actions/cache@v3
  with:
    path: ~/.m2/repository
    key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}
    restore-keys: |
      ${{ runner.os }}-maven-

# Upload build artifacts
- name: Upload test results
  if: always()  # Even if tests fail
  uses: actions/upload-artifact@v3
  with:
    name: test-results-${{ matrix.python-version }}
    path: test-results/

# Download artifact from previous job
- name: Download test results
  uses: actions/download-artifact@v3
  with:
    name: test-results-3.11
```

## Deployment Strategies

### Blue-Green: Zero-Downtime Switch
```yaml
# Deploy to GREEN environment (idle)
# Run full tests against GREEN
# Switch traffic from BLUE → GREEN
# BLUE becomes idle, ready for next deployment

- name: Deploy to green environment
  run: |
    kubectl set image deployment/api-green \
      api=myregistry.azurecr.io/api:${{ github.sha }}

- name: Health check green
  run: |
    kubectl rollout status deployment/api-green --timeout=5m

- name: Switch traffic (blue → green)
  run: |
    kubectl patch service api \
      -p '{"spec":{"selector":{"version":"green"}}}'
```

### Canary: Gradual Rollout
```yaml
# Deploy to 10% of users, monitor
# If no errors, ramp to 25%, then 50%, then 100%

- name: Deploy canary (10%)
  run: |
    kubectl set image deployment/api-canary \
      api=myregistry.azurecr.io/api:${{ github.sha }}
    kubectl patch service api \
      -p '{"spec":{"selector":{"version":"canary"}}}' --overwrite

- name: Wait and monitor (10 min)
  run: sleep 600

- name: Check error rate
  run: |
    ERROR_RATE=$(curl prometheus.example.com/api/query \
      'rate(http_errors[5m])')
    if [ $(echo "$ERROR_RATE > 0.01" | bc) ]; then
      exit 1  # Rollback
    fi

- name: Ramp to 50%
  run: |
    # Update traffic split: 50% canary, 50% stable
    kubectl patch virtualservice api --type json \
      -p '[{"op":"replace","path":"/spec/hosts/0/http/0/route/0/weight","value":50}]'
```

### Rolling Update: Sequential Replacement
```yaml
# Update 1 pod at a time, maintain availability

- name: Apply rolling update
  run: |
    kubectl set image deployment/api \
      api=myregistry.azurecr.io/api:${{ github.sha }} \
      --record

    # Kubernetes does rolling update automatically:
    # 1. Create new pod v2
    # 2. Old pod v1 still serving traffic
    # 3. New pod v2 healthy? Remove v1
    # 4. Repeat for remaining pods
    # At no point is service unavailable

    kubectl rollout status deployment/api --timeout=10m
```

## Feature Flags (LaunchDarkly/Unleash)

```yaml
# Deploy incomplete feature behind flag

- name: Deploy new checkout feature
  run: |
    docker build -t myapp:${{ github.sha }} .
    docker push myregistry.azurecr.io/myapp:${{ github.sha }}
    kubectl set image deployment/api \
      api=myregistry.azurecr.io/myapp:${{ github.sha }}

# Code uses feature flag
def process_checkout():
    if feature_flag("new_checkout"):
        return new_checkout_flow()  # Incomplete, but only 5% see it
    else:
        return legacy_checkout_flow()

# In dashboard: Gradually enable flag
# 0% → 5% → 25% → 50% → 100%
# Kill switch available instantly if problems
```

## Database Migrations in CI/CD

```yaml
# Never block deployment on slow migrations

- name: Start database migration
  run: |
    # Async start (don't wait)
    kubectl exec -i deployment/migration-job \
      -- migration_script.sh &

- name: Deploy new code
  run: |
    kubectl set image deployment/api \
      api=myregistry.azurecr.io/api:${{ github.sha }}
    # Code handles both old and new schema (expand-contract)

- name: Wait for migration to complete
  run: |
    for i in {1..60}; do
      if [ $(migration_status) == "complete" ]; then
        exit 0
      fi
      sleep 10
    done
    exit 1  # Migration timeout
```

## Secret Rotation

```yaml
- name: Rotate database password
  run: |
    # 1. Generate new password
    NEW_PASSWORD=$(openssl rand -base64 32)

    # 2. Update database
    mysql -h ${{ secrets.DB_HOST }} \
      ALTER USER 'api' IDENTIFIED BY '$NEW_PASSWORD';

    # 3. Update secret in GitHub
    curl -H "Authorization: token ${{ secrets.GITHUB_TOKEN }}" \
      -X PATCH \
      -d '{"encrypted_value":"..."}' \
      https://api.github.com/repos/.../secrets/DB_PASSWORD

    # 4. Restart pods (picks up new secret from env)
    kubectl rollout restart deployment/api

    # Schedule next rotation in 90 days
    # (GitHub has automatic rotation)
```

## CI/CD Checklist

```markdown
# CI/CD Checklist

## Pipeline
- [ ] Tests run on every push
- [ ] Linting/formatting checked
- [ ] Security scanning enabled
- [ ] Code coverage >80%

## Artifacts
- [ ] Docker images cached
- [ ] Build outputs stored
- [ ] Failed test results captured

## Deployment
- [ ] Blue-green or canary strategy
- [ ] Health checks before promoting
- [ ] Rollback plan documented
- [ ] Feature flags for risky features

## Secrets
- [ ] No hardcoded secrets in repo
- [ ] Secrets rotated regularly
- [ ] Access audit trail
- [ ] Multi-environment isolation

## Monitoring
- [ ] Error rate tracked
- [ ] Performance metrics collected
- [ ] Alerts configured
- [ ] Incident response documented
```

## Summary
CI/CD automates testing, building, and deployment. Use matrix builds for multi-version testing. Cache dependencies and artifacts. Deploy with blue-green, canary, or rolling strategies. Hide incomplete features behind flags. Migrate databases without downtime. Rotate secrets regularly. Automate everything.

---
> Source: [AniruddhaPKawarase/ai-agent-skills-toolkit](https://github.com/AniruddhaPKawarase/ai-agent-skills-toolkit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
