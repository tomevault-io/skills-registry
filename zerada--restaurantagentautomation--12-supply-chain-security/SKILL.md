---
name: supply-chain-security
description: Docker image signing (Cosign), SBOM generation, SLSA provenance attestation, GHCR registry management. Use when this capability is needed.
metadata:
  author: zerada
---

# Supply Chain Security (RESTO BOT)

## Docker image pipeline

- Build: `project/.github/workflows/build-push-artifacts.yml`
- Registry: GitHub Container Registry (GHCR)
- Images built: gateway, admin-dashboard, kiosk-app, cms (Strapi)

## Signing and attestation

- **Cosign**: Image signing with key pair
  - Secrets: COSIGN_PASSWORD, COSIGN_PRIVATE_KEY (GitHub Actions secrets)
  - Verify: `cosign verify --key cosign.pub <image>`
- **SBOM**: Software Bill of Materials generated per build
- **SLSA Provenance**: Attestation for build reproducibility

## SHA-pinning policy

- All GitHub Actions must use SHA-pinned references
- Example: `uses: actions/checkout@sha256:...` (not `@v4`)
- Enforced in CI lint step

## Version consistency

- `N8N_VERSION` must match across:
  - `project/.env`
  - `project/.github/workflows/ci.yml`
  - `project/.github/workflows/security-scan.yml`
- Base images pinned in Dockerfiles and compose

## Image inventory

| Image | Source | Signed |
| --- | --- | --- |
| nginx:1.27-alpine | Docker Hub | N/A (upstream) |
| n8n:1.80.0 | docker.n8n.io | N/A (upstream) |
| postgres:15-alpine | Docker Hub | N/A (upstream) |
| redis:7-alpine | Docker Hub | N/A (upstream) |
| traefik:v3.6.6 | Docker Hub | N/A (upstream) |
| ollama:0.6.2 | Docker Hub | N/A (upstream) |
| gateway (custom) | GHCR | Cosign |
| admin-dashboard (custom) | GHCR | Cosign |
| kiosk-app (custom) | GHCR | Cosign |
| cms (custom) | GHCR | Cosign |

## Key files

- `project/.github/workflows/build-push-artifacts.yml`
- `project/.github/workflows/security-scan.yml` (Trivy)
- `project/admin-dashboard/Dockerfile`
- `project/kiosk-app/Dockerfile`
- `project/inventory-cms/Dockerfile`

## Required output

- Signing verification evidence
- SBOM generation confirmation
- SHA-pin audit for GitHub Actions
- Version consistency check

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zerada) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
