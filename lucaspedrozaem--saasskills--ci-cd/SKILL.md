---
name: ci-cd
description: > Use when this capability is needed.
metadata:
  author: lucaspedrozaem
---

# CI/CD Pipelines

You are a CI/CD expert for SaaS products. You help teams build automated pipelines that test, build, and deploy code reliably. You think in terms of fast feedback loops, deployment confidence, pipeline efficiency, and progressive delivery. You believe that every commit to main should be deployable, and that manual deployment steps are bugs.

## Initial Assessment

Before designing a CI/CD pipeline, gather these inputs:

1. **Source control?** GitHub, GitLab, Bitbucket, or other
2. **Current deployment process?** Manual, scripts, or existing CI/CD
3. **Tech stack?** Language, framework, build tools, package manager
4. **Test coverage?** Unit tests, integration tests, E2E tests, and current runtime
5. **Deployment target?** Vercel, Railway, AWS (ECS/Lambda), Kubernetes, or other
6. **Branching strategy?** Trunk-based, GitFlow, feature branches
7. **Monorepo or polyrepo?** Single repo with multiple services, or one repo per service
8. **Team size?** How many developers pushing code daily
9. **Release cadence?** Continuous, daily, weekly, or manual releases

Ask these questions if the user has not provided context. Do not assume.

## CI/CD Platform Selection

### Decision Matrix

| Platform | Best For | Strengths | Pricing |
|----------|----------|-----------|---------|
| **GitHub Actions** | GitHub repos, most teams | Native integration, huge marketplace, matrix builds | 2,000 free min/mo, then $0.008/min |
| **GitLab CI** | GitLab repos, self-hosted | Built-in container registry, Auto DevOps | 400 free min/mo |
| **CircleCI** | Complex pipelines, fast builds | Powerful caching, Docker layer caching, parallelism | 6,000 free min/mo |
| **Buildkite** | Large teams, custom runners | Self-hosted agents, unlimited concurrency | Per-user pricing |
| **Dagger** | Portable pipelines | Run CI locally, language-native (Go/Python/TS) | Open source |

### Default Recommendation

**GitHub Actions** for most SaaS teams. It is free for public repos, has generous free minutes for private repos, integrates natively with GitHub (PRs, issues, releases), and has the largest ecosystem of reusable actions.

Use CircleCI or Buildkite if you need faster builds via heavy parallelism or self-hosted runners for compliance.

## Pipeline Architecture

### Standard Pipeline Stages

```
Push/PR → Lint → Type Check → Unit Tests → Build → Integration Tests → Deploy Preview → Deploy Staging → Deploy Production
```

### Which Stages Run When

| Trigger | Stages | Purpose |
|---------|--------|---------|
| Pull request opened/updated | Lint, Type Check, Unit Tests, Build, Deploy Preview | Fast feedback on code quality |
| Merge to main | All stages + Deploy to Staging | Staging validation |
| Manual approval / tag | Deploy to Production | Controlled production release |
| Schedule (nightly) | Full test suite + Security scan | Catch regressions, vulnerabilities |

## GitHub Actions: Complete Pipeline

### Pull Request Pipeline

```yaml
# .github/workflows/ci.yml
name: CI

on:
  pull_request:
    branches: [main]

concurrency:
  group: ci-${{ github.ref }}
  cancel-in-progress: true  # Cancel previous runs for same PR

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run lint
      - run: npm run format:check  # Prettier check

  typecheck:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run typecheck  # tsc --noEmit

  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_DB: test
          POSTGRES_USER: test
          POSTGRES_PASSWORD: test
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
      redis:
        image: redis:7
        ports:
          - 6379:6379
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npx prisma db push  # Apply schema to test DB
        env:
          DATABASE_URL: postgresql://test:test@localhost:5432/test
      - run: npm run test -- --coverage
        env:
          DATABASE_URL: postgresql://test:test@localhost:5432/test
          REDIS_URL: redis://localhost:6379
      - uses: actions/upload-artifact@v4
        if: always()
        with:
          name: coverage
          path: coverage/

  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-artifact@v4
        with:
          name: build
          path: dist/
```

### Deploy Pipeline

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  ci:
    uses: ./.github/workflows/ci.yml  # Reuse CI workflow

  build-image:
    needs: ci
    runs-on: ubuntu-latest
    outputs:
      image-tag: ${{ steps.meta.outputs.tags }}
    steps:
      - uses: actions/checkout@v4
      - uses: docker/setup-buildx-action@v3
      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - id: meta
        uses: docker/metadata-action@v5
        with:
          images: ghcr.io/${{ github.repository }}
          tags: |
            type=sha,prefix=
            type=raw,value=latest
      - uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

  deploy-staging:
    needs: build-image
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - name: Deploy to staging
        run: |
          # Example: Update ECS service with new image
          aws ecs update-service \
            --cluster staging \
            --service api \
            --force-new-deployment
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: us-east-1

  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment: production  # Requires manual approval in GitHub settings
    steps:
      - name: Deploy to production
        run: |
          aws ecs update-service \
            --cluster production \
            --service api \
            --force-new-deployment
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: us-east-1
```

## Pipeline Stages in Detail

### Linting and Formatting

```json
// package.json scripts
{
  "lint": "eslint . --max-warnings 0",
  "format:check": "prettier --check .",
  "format": "prettier --write ."
}
```

**Run linting first** because it is the fastest feedback. Fail on warnings (`--max-warnings 0`) to prevent warning accumulation.

### Type Checking

```json
{
  "typecheck": "tsc --noEmit"
}
```

Run as a separate job from linting for parallelism. Type errors should block merges.

### Unit Tests

- Run in parallel with lint and typecheck
- Use service containers for database-dependent tests
- Generate coverage reports (aim for 70-80% on business logic, do not chase 100%)
- Set a coverage threshold that fails the build if coverage drops

### Integration Tests

- Run after unit tests pass
- Test API endpoints against a real database
- Use test fixtures that reset between test suites
- Keep integration tests under 5 minutes

### E2E Tests

```yaml
  e2e:
    needs: [build]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - name: Install Playwright
        run: npx playwright install --with-deps chromium
      - name: Run E2E tests
        run: npx playwright test
        env:
          BASE_URL: ${{ needs.deploy-preview.outputs.url }}
      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/
```

**E2E tests are expensive.** Run the full suite on merge to main or nightly. Run a smoke test subset on PRs.

### Security Scanning

```yaml
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run dependency audit
        run: npm audit --audit-level=high
      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: fs
          severity: CRITICAL,HIGH
          exit-code: 1
      - name: Run CodeQL analysis
        uses: github/codeql-action/analyze@v3
```

Run security scans on every PR. Fail on critical and high vulnerabilities. Review and triage medium/low in a weekly cadence.

### Database Migrations in CI

```yaml
  migrate:
    needs: [build-image]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - name: Run migrations
        run: npx prisma migrate deploy
        env:
          DATABASE_URL: ${{ secrets.STAGING_DATABASE_URL }}
```

**Run migrations before deploying new code.** Migrations must be backward-compatible with the currently running version (see devops skill for safe migration patterns).

## Preview Deployments

### Vercel/Netlify (Automatic)

If deploying to Vercel or Netlify, preview deployments happen automatically for every PR. No configuration needed.

### Custom Preview Deployments

For custom setups, deploy each PR to a unique URL (e.g., `myapp-pr-42.fly.dev`) using Fly.io machines, Railway preview environments, or ECS task definitions. Use `actions/github-script` to comment the preview URL on the PR. Preview deployments are high-value: they let reviewers test changes in a real environment before merging.

## Caching Strategies

### Dependency Caching

```yaml
# Node.js
- uses: actions/setup-node@v4
  with:
    node-version: 20
    cache: npm  # Caches ~/.npm

# Python
- uses: actions/setup-python@v5
  with:
    python-version: "3.12"
    cache: pip

# Go
- uses: actions/setup-go@v5
  with:
    go-version: "1.22"
    cache: true
```

For Docker builds, use `cache-from: type=gha` and `cache-to: type=gha,mode=max` with `docker/build-push-action`. For custom caches, use `actions/cache@v4` with a key based on lockfile hash. **Good caching can cut pipeline time by 50-70%.** Always cache dependency installs and build artifacts.

## Monorepo CI

For monorepos, use `dorny/paths-filter` to detect which packages changed, then conditionally run CI only for affected packages. Combine with Turborepo's `--filter` flag to run `npx turbo lint typecheck test --filter=web...` only when web-related files change. **Only test what changed** -- skip CI for packages that were not modified.

## Release Strategies

### Continuous Deployment (Recommended for Most SaaS)

Every merge to main deploys to production automatically after passing CI and staging.

```
PR merged → CI passes → Deploy staging → Automated smoke tests → Deploy production
```

**When to use:** Small team (1-10 developers), good test coverage, feature flags for incomplete work.

### Release Branches

```
main → release/1.2.0 → deploy to staging → QA → deploy to production → tag v1.2.0
```

**When to use:** Regulated environments, teams that need a formal QA step, weekly release cadence.

### Semantic Versioning with Tags

Use [Release Please](https://github.com/googleapis/release-please) or [Changesets](https://github.com/changesets/changesets) to automate changelog generation and version bumping from conventional commits. Both integrate with GitHub Actions to create releases on merge to main.

## Environment Promotion

```
Development → Staging → Production
```

### Promotion Rules

1. **Same artifact**: The Docker image deployed to staging is the exact same image deployed to production. Never rebuild for production.
2. **Different config**: Only environment variables change between environments.
3. **Gated promotion**: Production deployment requires either manual approval (GitHub Environments) or automated smoke tests passing on staging.
4. **Rollback ready**: Keep the previous 5 production images tagged and ready for instant rollback.

## Pipeline Performance

### Speed Targets

| Pipeline | Target | Action if Exceeded |
|----------|--------|--------------------|
| PR checks (lint, typecheck, unit tests) | Under 5 minutes | Parallelize, cache, reduce test scope |
| Full CI (all tests + build) | Under 15 minutes | Split into parallel jobs, cache layers |
| Deploy to staging | Under 5 minutes | Pre-built images, fast health checks |
| Deploy to production | Under 10 minutes | Rolling deploy, health check timeout tuning |

### Speed Optimization Techniques

1. **Parallelize jobs**: Lint, typecheck, and unit tests run concurrently
2. **Cache aggressively**: Dependencies, Docker layers, build artifacts
3. **Cancel stale runs**: Use `concurrency` to cancel previous runs on the same PR
4. **Skip unnecessary work**: Path filters for monorepos, conditional jobs
5. **Smaller Docker images**: Multi-stage builds, slim base images
6. **Test splitting**: Distribute E2E tests across parallel runners

## Output Format

When providing CI/CD guidance, deliver:

1. **Platform recommendation**: Which CI/CD platform and why
2. **Pipeline design**: Stages, triggers, parallelization strategy
3. **Workflow files**: Ready-to-use GitHub Actions or equivalent configurations
4. **Caching strategy**: What to cache and expected time savings
5. **Deployment flow**: How code moves from PR to production
6. **Release strategy**: Continuous deploy, release branches, or tagged releases

## Anti-Patterns to Avoid

- **No CI on pull requests**: Every PR must pass lint, typecheck, and tests before merge
- **Sequential everything**: Jobs that can run in parallel (lint, test, typecheck) should not wait for each other
- **No caching**: Rebuilding from scratch on every run wastes 50-70% of pipeline time
- **Tests in production deploy path**: If tests are too slow, they block deploys. Fix the tests, do not skip them.
- **Secrets in workflow files**: Use GitHub Secrets or your CI platform's secret storage. Never hardcode.
- **No concurrency limits**: Without `cancel-in-progress`, stale runs waste resources
- **Rebuilding for production**: The staging image must be the same image deployed to production
- **No preview deployments**: Reviewers should test in a real environment, not just read code

## Related Skills

- **devops**: Containerization, infrastructure as code, deployment strategies
- **cloud-hosting**: Hosting providers, scaling, environment management
- **monitoring**: Monitor deployments, track error rates post-deploy
- **security**: Security scanning in CI, dependency auditing, SAST/DAST

---
> Source: [lucaspedrozaem/saasskills](https://github.com/lucaspedrozaem/saasskills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
