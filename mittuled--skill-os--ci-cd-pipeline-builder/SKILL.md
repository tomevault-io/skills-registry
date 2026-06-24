---
name: ci-cd-pipeline-builder
description: Builds reliable CI/CD pipelines that turn every commit into a deployable, tested artifact. Use when asked to ci cd pipeline builder. Suggest when relevant.
metadata:
  author: mittuled
---

# ci-cd-pipeline-builder

## Agent: Social Media Manager

L2 DevOps and infrastructure engineer responsible for CI/CD pipelines, deployment automation, cloud infrastructure, monitoring, alerting, incident response, and rollout management.

Department ethos: [ideal-engineering.md](../../../../departments/engineering/ideal-engineering.md)

## Skill Description

The DevOps / Infrastructure Engineer builds and configures continuous integration and continuous deployment pipelines.

## When to Use

- A new project or service needs a CI/CD pipeline from scratch.
- An existing pipeline is unreliable, slow, or missing stages (lint, test, security scan, deploy).
- The team is migrating to a new CI/CD platform or changing deployment targets.
- A post-incident review identified pipeline gaps that allowed a defect into production.

## Workflow

1. **Requirements and Strategy**: Gather target environments, deployment cadence, test suites, security scan requirements, and artifact storage. Select a deployment strategy based on risk tolerance: **blue-green** (zero-downtime swap between two identical environments), **canary** (route 1-5% of traffic to the new version, monitor error rates and p99 latency before full promotion), or **rolling** (incremental pod/instance replacement with readiness probes). Deliverable: pipeline requirements document with deployment strategy selection.
2. **Build and Test Stages**: Configure the CI platform (GitHub Actions, GitLab CI, CircleCI) with pipeline-as-code. Structure stages as: source checkout, dependency install with layer caching (Docker layer cache, npm/pip cache keyed by lockfile hash), parallel lint + unit test + SAST scan (Snyk, Trivy, or Semgrep), integration tests against ephemeral service containers, and artifact build with immutable image tagging (git SHA, not `latest`). Deliverable: pipeline definition files committed to the repository.
3. **Quality Gates**: Fail the pipeline on: lint errors, test failures, SAST findings above severity threshold (critical/high block, medium warn), coverage drops below baseline, and container image CVEs. Implement **feature flag** gating: deployments with risky changes ship behind flags (LaunchDarkly, Unleash, or environment variables) so the code deploys but the behavior is disabled until explicitly enabled. Deliverable: quality gate configuration with pass/fail criteria.
4. **Deployment Stages**: For blue-green: deploy to the inactive environment, run smoke tests against it, then swap the load balancer target group. For canary: deploy to the canary target group, configure weighted routing (AWS ALB weights, Istio VirtualService, or NGINX split_clients), monitor for 10-15 minutes, then promote or roll back automatically based on error rate and latency thresholds. Include automated **rollback**: if post-deploy health checks fail (HTTP 200 on `/healthz`, dependency connectivity, baseline metric comparison), revert the load balancer swap or scale the canary to zero. Deliverable: deployment stage configuration with rollback triggers.
5. **Secrets and Environment Configuration**: Store secrets in the CI platform's secret store (GitHub Secrets, Vault, AWS Secrets Manager) — never in pipeline YAML. Use environment-scoped secrets so staging and production credentials are isolated. Inject secrets as environment variables at runtime, not build time. Deliverable: secrets management configuration.
6. **Observability and Notifications**: Emit pipeline metrics (build duration, success rate, flaky test frequency, deployment frequency, lead time for changes) to a dashboard (Datadog, Grafana). Send failure notifications to Slack or PagerDuty with direct links to the failed stage logs. Deliverable: monitoring dashboard and notification configuration.
7. **End-to-End Validation**: Test the full pipeline with a representative change including a feature-flagged deployment. Verify rollback triggers work by intentionally deploying a failing health check. Deliverable: validation report confirming all stages, quality gates, deployment strategy, and rollback behavior.

## Anti-Patterns

- **Building pipelines without quality gates.** *Why*: A pipeline that deploys regardless of test results is an automated way to ship bugs faster.
- **Hardcoding secrets in pipeline definitions.** *Why*: Secrets in code are visible to anyone with repo access and persist in version history forever.
- **Ignoring pipeline performance.** *Why*: Slow pipelines reduce deployment frequency, encourage batching changes, and increase the blast radius of each deploy.
- **Skipping the staging deploy step.** *Why*: Deploying directly to production removes the last safety net for catching environment-specific failures.
- **Not monitoring pipeline health.** *Why*: A silently degrading pipeline (increasing flakiness, slower builds) erodes developer trust and slows delivery.

## Output

**Success**: A fully operational CI/CD pipeline that builds, tests, scans, and deploys code through staging to production with quality gates at each stage.

**Failure**: A pipeline assessment listing which stages are missing or broken, with a remediation plan and priority order.

## Related Skills

*None defined yet.*
- [`infrastructure-scaling-executor`](../infrastructure-scaling-executor/SKILL.md) — sibling skill under the same agent — combine with infrastructure-scaling-executor for end-to-end coverage
- [`alerting-configurator`](../alerting-configurator/SKILL.md) — sibling skill under the same agent — combine with alerting-configurator for end-to-end coverage
- [`production-readiness-reviewer`](../production-readiness-reviewer/SKILL.md) — sibling skill under the same agent — combine with production-readiness-reviewer for end-to-end coverage

---
> Source: [mittuled/skill-os](https://github.com/mittuled/skill-os) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
