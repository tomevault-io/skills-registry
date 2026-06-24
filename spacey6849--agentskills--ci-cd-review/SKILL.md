---
name: ci-cd-review
description: Review CI/CD pipeline configuration — build reliability, test integration, deployment safety, and feedback loop speed. Use when this capability is needed.
metadata:
  author: Spacey6849
---

## Instructions

You are reviewing a CI/CD pipeline configuration. A good pipeline is fast, reliable, and catches problems before they reach production.

### Pipeline Structure

- Verify the pipeline has distinct stages: lint → build → test → deploy.
- Each stage MUST fail fast — do not run expensive steps if cheap checks fail.
- Check that the pipeline runs on every PR and on merge to the main branch.
- Verify branch protection rules prevent merging without passing CI.

### Build Stage

- Verify builds are reproducible — same commit MUST produce the same artifact.
- Check that dependencies are installed from a lockfile, not floating ranges.
- Flag builds that take more than 10 minutes — identify what is slow and suggest caching, parallelization, or incremental builds.
- Verify build artifacts are stored (not rebuilt for deployment).
- Check for proper caching of dependencies and intermediate build outputs.

### Test Stage

- Verify unit tests run on every PR.
- Check for integration test coverage in the pipeline.
- Flag test suites that take more than 10 minutes — suggest parallelization or test splitting.
- Verify flaky test detection and quarantine mechanisms exist.
- Check that test failures block merge — tests MUST NOT be advisory-only.

### Deployment Stage

- Verify deployments use immutable artifacts (deploy what was built, not rebuild).
- Check for environment promotion strategy (dev → staging → production).
- Verify staging environment is representative of production.
- Flag deployments that go directly to production without a staging step.
- Check for deployment approval gates on production.

### Safety Checks

- Verify secrets are not exposed in CI logs.
- Check that CI runners have minimal permissions (least privilege).
- Flag `sudo` or elevated permissions in CI scripts without justification.
- Verify pull requests from forks do not have access to secrets.
- Check dependency scanning (Dependabot, Snyk, Renovate) is configured.
- Verify container image scanning if Docker is used.

### Pipeline Reliability

- Flag non-deterministic steps (network-dependent downloads without pinned versions, flaky tests).
- Verify pipeline can be re-run safely (idempotent steps).
- Check for proper timeout configuration — prevent hung pipelines from consuming resources indefinitely.
- Verify notifications on failure reach the right people.

### Output

- List each finding with severity and recommended fix.
- Provide time estimates for optimizations (e.g., "Adding caching should reduce build time from 8min to 2min").
- Prioritize safety issues over speed issues.

---
> Source: [Spacey6849/AgentSkills](https://github.com/Spacey6849/AgentSkills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
