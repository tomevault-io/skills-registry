---
name: docker-image-publishing
description: multi-arch image publishing to GHCR via GitHub Actions Use when this capability is needed.
metadata:
  author: rcarmo
---

# Skill: Docker image publishing

## Goal
Provide a reusable GitHub Actions workflow to build/push multi-arch images to GHCR on tag pushes.

## Conventions
- Publish on tags `v*`.
- Use native Intel or ARM runners for CI/CD. Don't use QEMU
- Use buildx + per-arch digest builds, then merge into a manifest.
- Use `docker/metadata-action` for semver tag derivation.

## Files
- `.github/workflows/docker-publish.yml`
- `.github/workflows/prune-docker-images.yml`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rcarmo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
