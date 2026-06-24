---
name: ci-cd-pipeline
description: CI/CD pipeline patterns for GitHub Actions and GitLab CI. Activates when editing .github/workflows/*, .gitlab-ci.yml, or when auditing or debugging a pipeline. Use when this capability is needed.
metadata:
  author: felixhennequin-gif
---

# CI/CD pipelines (GitHub Actions + GitLab CI)

Focused on the four rules that matter in real pipelines. Syntax details live in the upstream docs — this file only captures the conventions teams get wrong by default.

## Rules

1. **Build once, deploy many.** Produce one artifact tagged with the commit SHA, then promote it across environments. Rebuilding per environment is how staging and prod drift.
2. **Idempotence.** Same commit → same artifact. Use `npm ci` / `pip install --require-hashes` / `cargo build --locked`, pin Docker base images to an explicit version, and pin third-party actions by full 40-char SHA (not `@v4`, not `@main`).
3. **Fail fast under 10 min.** Run lint, unit tests, and security scans in parallel jobs. A sequential pipeline is the biggest avoidable latency in CI.
4. **Separate build from deploy.** Two jobs, two responsibilities. The build job never touches deploy credentials.

## GOOD vs BAD

```yaml
# BAD — action pinned by tag, image rebuilt per env
- uses: actions/checkout@v4
- run: docker build -t my-app:staging .    # and again for prod

# GOOD — action pinned by SHA, image built once with SHA tag
- uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683  # v4
- run: docker build -t my-app:${{ github.sha }} .
```

## Secrets and credentials

- Prefer OIDC over long-lived API keys (GitHub Actions → AWS/GCP/Azure role assumption, GitLab `id_tokens:`).
- Scope secrets per environment: `environment: staging` and `environment: production` get different secret stores.
- Never `echo $SECRET`, never pass a secret as a CLI argument on a shared runner.
- The minimum-permissions default for a GitHub workflow is `permissions: contents: read`. Add write scopes per-job only when they're needed.

## Parallelism

- **GitHub Actions**: use `needs:` to build a DAG. Jobs with `needs: []` start immediately; jobs with `needs: [lint, test]` wait for both.
- **GitLab CI**: `needs:` turns stages into a DAG (no longer strictly sequential). `parallel:` runs matrix variants side-by-side.

## Pipeline audit checklist

Use this when reviewing an existing workflow:

- [ ] Every third-party action pinned by 40-char SHA
- [ ] Docker base images pinned to explicit version (not `:latest`, not `:lts`)
- [ ] Top-level `permissions:` declared and minimal
- [ ] Secrets scoped per environment; no production secrets in build jobs
- [ ] OIDC used instead of static cloud keys where the target cloud supports it
- [ ] `npm ci` / `pip install --require-hashes` / `cargo build --locked` — no unlocked installs
- [ ] Lint, test, and security scan run in parallel, not sequentially
- [ ] Rollback procedure documented or scripted

## Helper script

`scripts/action-pin-check.sh <workflow.yml>...` — scans workflows for `uses:` references that aren't full SHAs. Wire it into CI so SHA pinning is enforced, not aspirational.

## Anti-patterns

- ❌ `uses: foo/bar@v4` or `@main` — pin by SHA
- ❌ `docker build -t my-app:staging` and a separate `my-app:prod` build — build once, retag on promotion
- ❌ `npm install` in CI — use `npm ci`
- ❌ `:latest` or `:lts` image tags — pin to an exact version
- ❌ One giant sequential pipeline — split into parallel jobs with `needs:`
- ❌ Production secrets exported in the build job — scope per environment
- ❌ `echo $SECRET` or secrets in `set -x` output — they end up in logs

## References

- GitHub Actions: https://docs.github.com/en/actions
- GitLab CI: https://docs.gitlab.com/ee/ci/
- OIDC for cloud auth: https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect

---
> Source: [felixhennequin-gif/claude-code-config-template](https://github.com/felixhennequin-gif/claude-code-config-template) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
