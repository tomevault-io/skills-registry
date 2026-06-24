---
name: release-flow
description: Release pipeline patterns — python-semantic-release with conventional commits, PyPI trusted publishing, Docker multi-arch builds with GHCR, GitHub Actions workflows, and version tagging strategy Use when this capability is needed.
metadata:
  author: pvliesdonk
---

## Pipeline Overview

```
Conventional Commits → semantic-release → Tag + Changelog
                                        → PyPI publish (trusted publishing)
                                        → Docker build + push (GHCR)
                                        → GitHub Release with assets
```

## Conventional Commits → Version Bumps

| Prefix | Bump | Example |
|--------|------|---------|
| `fix:` | PATCH | `fix(parser): handle empty YAML blocks` |
| `feat:` | MINOR | `feat(cli): add --json output flag` |
| `feat!:` or `BREAKING CHANGE:` | MAJOR | `feat!: restructure artifact schema` |
| `docs:`, `chore:`, `refactor:`, `test:` | No bump | `docs: update README` |

## python-semantic-release

Config in `pyproject.toml`:
```toml
[tool.semantic_release]
version_toml = ["pyproject.toml:project.version"]
branch = "main"
commit_message = "chore(release): {version}"
build_command = "uv build"

[tool.semantic_release.changelog]
changelog_file = "CHANGELOG.md"

[tool.semantic_release.remote]
type = "github"
```

## GitHub Actions Release Workflow

Three-job pipeline (release → publish-pypi → publish-docker):

### Job 1: Release (version bump + tag)
- `python-semantic-release/python-semantic-release@v10`
- Needs: `contents: write`, `id-token: write`
- Uses `RELEASE_TOKEN` (PAT) for pushing tags
- Outputs: `released`, `version`, `tag`

### Job 2: PyPI (trusted publishing)
- Triggered by `needs.release.outputs.released == 'true'`
- `pypa/gh-action-pypi-publish@release/v1`
- Uses `id-token: write` for OIDC trusted publishing (no API keys)
- Environment: `pypi` (configured in repo settings with PyPI project URL)

### Job 3: Docker (multi-arch GHCR)
- Checks out at the release tag
- Multi-platform: `linux/amd64,linux/arm64`
- Tags: `latest`, `vX.Y.Z`, `vX.Y`, `vX`
- Uses GHA cache for layer caching
- Generates build provenance attestation

## PyPI Trusted Publishing Setup

1. PyPI project settings → "Add a new publisher"
2. Set: GitHub owner, repo name, workflow filename, environment name (`pypi`)
3. No API keys needed — OIDC token from GitHub Actions

## Docker Tagging Strategy

For version `3.2.1`:
```
ghcr.io/owner/repo:latest    # Rolling latest
ghcr.io/owner/repo:v3.2.1    # Exact version
ghcr.io/owner/repo:v3.2      # Minor (for compat pinning)
ghcr.io/owner/repo:v3        # Major
```

## Version Bump in pyproject.toml

semantic-release handles this automatically. The version in `pyproject.toml` is the single source of truth. Never edit manually.

## Manual/Emergency Release

```bash
# Trigger via GitHub UI or CLI
gh workflow run release.yml -f force=patch  # or minor/major
```

## Pre-Release Checklist

1. All CI green on main
2. Conventional commits since last release
3. CHANGELOG reviewed (auto-generated but verify)
4. Docker build tested locally: `docker build -t test .`
5. No uncommitted changes on main

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pvliesdonk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
