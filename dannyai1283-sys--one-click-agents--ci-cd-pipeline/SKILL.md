---
name: ci-cd-pipeline
description: Deployment automation templates for continuous integration and delivery. Includes GitHub Actions, GitLab CI, Docker builds, and multi-environment deployment patterns. Use when this capability is needed.
metadata:
  author: dannyai1283-sys
---

# CI/CD Pipeline Templates

Ready-to-use CI/CD configurations for automated building, testing, and deployment. Works with GitHub Actions, GitLab CI, and other platforms.

## When to Use

- Setting up automated testing on every push
- Building and deploying Docker containers
- Managing multi-environment deployments
- Running security scans
- Automating releases
- Managing infrastructure as code

## Quick Start

```bash
# Setup CI/CD for your project
./setup.sh

# Choose your platform
./setup.sh github    # GitHub Actions
./setup.sh gitlab    # GitLab CI
./setup.sh docker    # Docker-based CI

# Configure deployment
./deploy.sh --env staging
./deploy.sh --env production
```

## GitHub Actions Templates

### Basic Node.js CI

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        node-version: [18.x, 20.x]
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linter
        run: npm run lint
      
      - name: Run tests
        run: npm test
      
      - name: Build
        run: npm run build
```

### Docker Build and Push

```yaml
# .github/workflows/docker.yml
name: Docker Build

on:
  push:
    tags: ['v*']
    branches: [main]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      
      - name: Login to Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      
      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            type=ref,event=branch
            type=semver,pattern={{version}}
            type=semver,pattern={{major}}.{{minor}}
      
      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

### Deploy to Production

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    tags: ['v*']
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: ${{ github.event.inputs.environment || 'staging' }}
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Setup kubectl
        uses: azure/setup-kubectl@v3
      
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1
      
      - name: Update kubeconfig
        run: aws eks update-kubeconfig --name my-cluster
      
      - name: Deploy
        run: |
          kubectl set image deployment/app \
            app=${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.ref_name }} \
            -n ${{ github.event.inputs.environment }}
          kubectl rollout status deployment/app -n ${{ github.event.inputs.environment }}
```

## GitLab CI Templates

### Basic Pipeline

```yaml
# .gitlab-ci.yml
stages:
  - test
  - build
  - deploy

variables:
  DOCKER_IMAGE: $CI_REGISTRY_IMAGE

# Cache node_modules
cache:
  paths:
    - node_modules/

# Run tests
test:
  stage: test
  image: node:20-alpine
  script:
    - npm ci
    - npm run lint
    - npm test
  coverage: '/All files[^|]*\|[^|]*\s+([\d\.]+)/'

# Build Docker image
build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker build -t $DOCKER_IMAGE:$CI_COMMIT_SHA .
    - docker push $DOCKER_IMAGE:$CI_COMMIT_SHA
    - docker tag $DOCKER_IMAGE:$CI_COMMIT_SHA $DOCKER_IMAGE:latest
    - docker push $DOCKER_IMAGE:latest

# Deploy to staging
deploy:staging:
  stage: deploy
  environment:
    name: staging
    url: https://staging.example.com
  script:
    - echo "Deploy to staging"
  only:
    - develop

# Deploy to production
deploy:production:
  stage: deploy
  environment:
    name: production
    url: https://example.com
  script:
    - echo "Deploy to production"
  only:
    - tags
  when: manual
```

## Deployment Patterns

### Blue-Green Deployment

```yaml
# .github/workflows/blue-green.yml
name: Blue-Green Deployment

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Determine active color
        id: color
        run: |
          CURRENT=$(kubectl get service app -o jsonpath='{.spec.selector.color}')
          if [ "$CURRENT" = "blue" ]; then
            echo "new=green" >> $GITHUB_OUTPUT
            echo "old=blue" >> $GITHUB_OUTPUT
          else
            echo "new=blue" >> $GITHUB_OUTPUT
            echo "old=green" >> $GITHUB_OUTPUT
          fi
      
      - name: Deploy to inactive environment
        run: |
          kubectl apply -f k8s/${{ steps.color.outputs.new }}-deployment.yml
          kubectl set image deployment/app-${{ steps.color.outputs.new }} \
            app=$IMAGE:${{ github.sha }}
          kubectl rollout status deployment/app-${{ steps.color.outputs.new }}
      
      - name: Health check
        run: |
          kubectl run health-check --rm -i --restart=Never \
            --image=curlimages/curl -- \
            http://app-${{ steps.color.outputs.new }}/health
      
      - name: Switch traffic
        run: |
          kubectl patch service app -p \
            '{"spec":{"selector":{"color":"${{ steps.color.outputs.new }}"}}}'
      
      - name: Scale down old version
        run: |
          kubectl scale deployment app-${{ steps.color.outputs.old }} --replicas=0
```

### Canary Deployment

```yaml
# .github/workflows/canary.yml
name: Canary Deployment

on:
  push:
    tags: ['v*']

jobs:
  canary:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy canary (10% traffic)
        run: |
          kubectl apply -f k8s/canary-deployment.yml
          kubectl set image deployment/app-canary app=$IMAGE:${{ github.sha }}
          kubectl scale deployment app-canary --replicas=1
          kubectl scale deployment app-stable --replicas=9
      
      - name: Wait and monitor
        run: sleep 300  # 5 minutes
      
      - name: Check error rate
        run: |
          ERRORS=$(kubectl logs deployment/app-canary | grep ERROR | wc -l)
          if [ $ERRORS -gt 10 ]; then
            echo "Too many errors, rolling back"
            kubectl scale deployment app-canary --replicas=0
            exit 1
          fi
      
      - name: Promote to full deployment
        run: |
          kubectl set image deployment/app-stable app=$IMAGE:${{ github.sha }}
          kubectl scale deployment app-stable --replicas=10
          kubectl scale deployment app-canary --replicas=0
```

## Security Scanning

### Dependency Check

```yaml
# .github/workflows/security.yml
name: Security Scan

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 6 * * 1'  # Weekly on Monday

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run npm audit
        run: npm audit --audit-level=moderate
      
      - name: Run Snyk
        uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
      
      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'sarif'
          output: 'trivy-results.sarif'
      
      - name: Upload scan results
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'
```

### Secret Detection

```yaml
- name: Secret Detection
  uses: trufflesecurity/trufflehog@main
  with:
    path: ./
    base: main
    head: HEAD
```

## Release Automation

### Semantic Release

```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    branches: [main]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          token: ${{ secrets.GH_TOKEN }}
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
      
      - name: Install dependencies
        run: npm ci
      
      - name: Release
        env:
          GITHUB_TOKEN: ${{ secrets.GH_TOKEN }}
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
        run: npx semantic-release
```

**`.releaserc.json`:**
```json
{
  "branches": ["main"],
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    "@semantic-release/npm",
    "@semantic-release/github",
    ["@semantic-release/git", {
      "assets": ["CHANGELOG.md", "package.json"],
      "message": "chore(release): ${nextRelease.version} [skip ci]\n\n${nextRelease.notes}"
    }]
  ]
}
```

## Environment Management

### Environment Variables

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main, develop]

jobs:
  set-env:
    runs-on: ubuntu-latest
    outputs:
      environment: ${{ steps.set.outputs.environment }}
      url: ${{ steps.set.outputs.url }}
    steps:
      - id: set
        run: |
          if [ "${{ github.ref }}" = "refs/heads/main" ]; then
            echo "environment=production" >> $GITHUB_OUTPUT
            echo "url=https://app.example.com" >> $GITHUB_OUTPUT
          else
            echo "environment=staging" >> $GITHUB_OUTPUT
            echo "url=https://staging.example.com" >> $GITHUB_OUTPUT
          fi

  deploy:
    needs: set-env
    runs-on: ubuntu-latest
    environment:
      name: ${{ needs.set-env.outputs.environment }}
      url: ${{ needs.set-env.outputs.url }}
    steps:
      - name: Deploy to ${{ needs.set-env.outputs.environment }}
        run: |
          echo "Deploying to ${{ needs.set-env.outputs.environment }}"
```

### Secrets Management

```bash
# Set secrets via CLI
github secrets set DATABASE_URL
github secrets set API_KEY
github secrets set SSH_PRIVATE_KEY

# View environment secrets
github secrets list --env production
```

## Best Practices

1. **Fast Feedback** - Run quick tests first, slow tests later
2. **Fail Fast** - Stop pipeline on first failure
3. **Parallel Jobs** - Run independent jobs in parallel
4. **Caching** - Cache dependencies and build artifacts
5. **Security** - Never commit secrets, use environment variables
6. **Idempotency** - Deployments should be repeatable
7. **Health Checks** - Verify deployments before marking success
8. **Rollback** - Have automated rollback procedures

## Local Testing

### Act - Run GitHub Actions Locally

```bash
# Install act
curl https://raw.githubusercontent.com/nektos/act/master/install.sh | bash

# Run default job
act

# Run specific job
act -j test

# Run with specific event
act push

# Use specific image
act -P ubuntu-latest=nektos/act-environments-ubuntu:18.04
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dannyai1283-sys) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
