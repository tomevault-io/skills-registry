---
name: ci-cd-optimizer
description: Creates and optimizes CI/CD pipelines for GitHub Actions, GitLab CI, and Jenkins. Use when setting up new pipelines, improving build times, or implementing security scanning.
metadata:
  author: brainupgrade-in
---

# CI/CD Pipeline Standards

## Overview

This skill helps create efficient, secure CI/CD pipelines following industry best practices for build optimization, security scanning, and deployment automation.

## GitHub Actions Template

### Basic CI Pipeline

```yaml
name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - run: npm ci
      - run: npm test
      - run: npm run build

      - uses: actions/upload-artifact@v4
        with:
          name: build-output
          path: dist/

  security:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          severity: 'CRITICAL,HIGH'
          exit-code: '1'

  deploy:
    needs: [build, security]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: build-output
          path: dist/

      # Deployment steps here
```

### Docker Build and Push

```yaml
  docker:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: docker/setup-buildx-action@v3

      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - uses: docker/build-push-action@v5
        with:
          context: .
          push: ${{ github.event_name != 'pull_request' }}
          tags: ghcr.io/${{ github.repository }}:${{ github.sha }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

## GitLab CI Template

```yaml
stages:
  - build
  - test
  - security
  - deploy

variables:
  DOCKER_TLS_CERTDIR: "/certs"

.node-cache: &node-cache
  cache:
    key: ${CI_COMMIT_REF_SLUG}
    paths:
      - node_modules/
      - .npm/

build:
  stage: build
  image: node:20-alpine
  <<: *node-cache
  script:
    - npm ci --cache .npm --prefer-offline
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 hour

test:
  stage: test
  image: node:20-alpine
  <<: *node-cache
  script:
    - npm ci --cache .npm --prefer-offline
    - npm test -- --coverage
  coverage: '/All files[^|]*\|[^|]*\s+([\d\.]+)/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml

security:
  stage: security
  image: aquasec/trivy:latest
  script:
    - trivy fs --exit-code 1 --severity HIGH,CRITICAL .
  allow_failure: false

deploy:
  stage: deploy
  image: alpine:latest
  script:
    - echo "Deploying to production"
  environment:
    name: production
    url: https://example.com
  only:
    - main
  when: manual
```

## Jenkins Pipeline (Declarative)

```groovy
pipeline {
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    environment {
        DOCKER_REGISTRY = 'docker.io'
        IMAGE_NAME = 'myorg/myapp'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm ci'
                sh 'npm run build'
            }
        }

        stage('Test') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        sh 'npm test'
                    }
                }
                stage('Lint') {
                    steps {
                        sh 'npm run lint'
                    }
                }
            }
        }

        stage('Security Scan') {
            steps {
                sh 'trivy fs --exit-code 1 --severity HIGH,CRITICAL .'
            }
        }

        stage('Docker Build') {
            when {
                branch 'main'
            }
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploying to production'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        failure {
            emailext subject: "Build Failed: ${env.JOB_NAME}",
                     body: "Check console output: ${env.BUILD_URL}",
                     to: 'team@example.com'
        }
    }
}
```

## Optimization Rules

### 1. Cache Dependencies

**Node.js:**
```yaml
- uses: actions/setup-node@v4
  with:
    cache: 'npm'
```

**Python:**
```yaml
- uses: actions/setup-python@v5
  with:
    cache: 'pip'
```

**Maven:**
```yaml
- uses: actions/setup-java@v4
  with:
    cache: 'maven'
```

### 2. Use Matrix Builds

```yaml
strategy:
  matrix:
    node: [18, 20, 22]
    os: [ubuntu-latest, windows-latest]
  fail-fast: true
```

### 3. Parallelize Independent Jobs

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    # ...

  test:
    runs-on: ubuntu-latest
    # Runs in parallel with lint

  build:
    needs: [lint, test]  # Waits for both
```

### 4. Fail Fast

```yaml
strategy:
  fail-fast: true  # Stop all jobs if one fails
```

### 5. Use Artifacts Efficiently

```yaml
- uses: actions/upload-artifact@v4
  with:
    retention-days: 5  # Reduce storage costs
    compression-level: 9  # Maximum compression
```

## Security Scanning Integration

### SAST (Static Analysis)
- CodeQL for GitHub
- SonarQube
- Semgrep

### DAST (Dynamic Analysis)
- OWASP ZAP
- Burp Suite

### Container Scanning
- Trivy
- Grype
- Snyk

### Dependency Scanning
- Dependabot
- Renovate
- npm audit

## Build Time Optimization Checklist

- [ ] Caching enabled for package managers
- [ ] Independent jobs run in parallel
- [ ] Fail-fast enabled for matrix builds
- [ ] Docker layer caching configured
- [ ] Minimal base images used
- [ ] Unnecessary steps removed
- [ ] Artifacts compressed and time-limited

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/brainupgrade-in) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
