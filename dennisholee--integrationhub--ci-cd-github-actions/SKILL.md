---
name: ci-cd-github-actions
description: CI/CD guidance and reusable GitHub Actions workflows for build, test, security scanning, and release. Use when this capability is needed.
metadata:
  author: dennisholee
---

# CI/CD and GitHub Actions

Purpose: Author reproducible CI workflows that enforce quality gates (build, tests, lint, security) and automate release steps.

Key topics:
- Workflow templates for PR validation and release pipelines
- Artifact build and publish steps (Maven/Gradle, Docker images)
- Security and dependency scanning integration (Snyk, oss-audit)
- Policy gates and required checks configuration

Required inputs:
- Repository build matrix, required checks list

Outputs:
- Reusable workflow YAMLs, status badge, CI runbooks

Success criteria:
- CI workflows run on PRs and produce deterministic results
- Blocking gates configured for tests and security scans
---
name: ci-cd-github-actions
description: CI/CD guidance and reusable GitHub Actions workflows for build, test, security scanning, and release.
---

# CI/CD and GitHub Actions

Purpose: Author reproducible CI workflows that enforce quality gates (build, tests, lint, security) and automate release steps.

Key topics:
- Workflow templates for PR validation and release pipelines
- Artifact build and publish steps (Maven/Gradle, Docker images)
- Security and dependency scanning integration (Snyk, oss-audit)
- Policy gates and required checks configuration

Quick GitHub Actions PR-validation snippet (example):
```yaml
name: PR Validation
on: [pull_request]
jobs:
	build:
		runs-on: ubuntu-latest
		strategy:
			matrix:
				java: [17, 21]
		steps:
			- uses: actions/checkout@v4
			- uses: actions/setup-java@v4
				with:
					java-version: ${{ matrix.java }}
			- name: Cache Maven
				uses: actions/cache@v4
				with:
					path: ~/.m2/repository
					key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}
			- name: Build & Test
				run: mvn -B -DskipTests=false verify
			- name: Run Snyk
				if: success()
				run: snyk test || true
```

Best practices:
- Keep PR workflows fast: run unit tests and quick integration smoke tests.
- Run longer integration tests and heavy scans on schedule or on-demand pipeline.
- Use caching for Maven/Gradle artifacts to speed up CI.

Security scanning integration:
- `snyk test` and `snyk monitor` can be included as steps; allow `snyk` to fail the job only on policy severity levels you enforce.
- Add `dependency-check` or `oss-audit` as supplementary scans.

Release & artifact publishing:
- Build Docker images in CI, tag with PR/branch identifiers, push to private registry in release pipelines.
- Publish Maven artifacts to repository manager (Nexus/Artifactory) from release workflows.

Policy gates:
- Configure branch protection rules to require passing checks: build, unit tests, security scan, and at least one approving review.

CI runbooks & troubleshooting:
- Document common failures (flaky tests, environment variables) and recommended remediation steps in `.github/ci/README.md`.

---
> Source: [dennisholee/IntegrationHub](https://github.com/dennisholee/IntegrationHub) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
