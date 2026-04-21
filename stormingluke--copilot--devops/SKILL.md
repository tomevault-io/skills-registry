---
name: devops
description: > Use when this capability is needed.
metadata:
  author: stormingluke
---

## CI/CD Patterns

### GitHub Actions
- Reusable workflows in `.github/workflows/`
- Use composite actions for shared steps
- Pin action versions by SHA, not tag
- Use OIDC for cloud provider authentication — no static secrets
- Cache Go modules: `actions/cache` with `go.sum` hash key
- Parallelise: lint, test, build as separate jobs
- Gate deployment behind test success

### Go Build Pipeline
```
lint (golangci-lint) → test (go test ./...) → build (go build) → image (ko/buildah) → sign (cosign) → deploy
```

### Container Images
- Use `ko` for Go services (no Dockerfile needed, reproducible)
- Use multi-stage builds if Dockerfile is required:
  ```dockerfile
  FROM golang:1.23 AS builder
  ...
  FROM gcr.io/distroless/static-debian12
  COPY --from=builder /app /app
  ```
- Tag with git SHA and semver
- Push to project's container registry (ACR, ECR, GAR, Quay)

### Release Automation
- Use `goreleaser` for CLI tools and binaries
- Semantic versioning with conventional commits
- Changelog generation from commit messages
- GitHub Releases with checksums and signatures
- Homebrew tap / APT repo for distribution if public

### Makefiles
- Standard targets: `make build`, `make test`, `make lint`, `make run`
- `make generate` for code generation (controller-gen, protobuf, etc.)
- `make manifests` for CRD/RBAC generation
- `make docker-build`, `make docker-push` for image lifecycle
- Use `.PHONY` for all non-file targets
- Include `help` target that auto-documents other targets

### Deployment
- GitOps: ArgoCD Application / ApplicationSet
- Promote via PR to deployment repo or overlay update
- Canary / blue-green via Argo Rollouts or Flagger
- Health checks: readiness and liveness probes required before deploy
- Rollback: automated via ArgoCD sync policy or manual `argocd app rollback`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stormingluke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
