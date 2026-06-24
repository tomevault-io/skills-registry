---
name: git-workflow
description: Squad.SDK.NET branch model: dev -> preview -> main with changesets Use when this capability is needed.
metadata:
  author: snickler
---

## Context

Squad.SDK.NET uses three long-lived branches:

| Branch | Purpose | Notes |
|--------|---------|-------|
| `dev` | Integration branch for feature work | PRs target `dev`; releaseable SDK changes add `.changeset` files |
| `preview` | Release candidate branch | Promotion from `dev` applies changesets into csproj version + changelog |
| `main` | Stable released branch | Pushes trigger automatic tagging and NuGet/GitHub release creation |

## Workflow for Issue Work

1. Branch from `dev`.
2. Do the work and add tests.
3. If you changed releaseable SDK behavior under `src/Squad.SDK.NET/`, add a `.changeset/*.md` file.
4. Open the PR against `dev`.
5. After merge, maintainers promote `dev -> preview` with `promote.yml`.
6. After preview validation passes, maintainers promote `preview -> main`.

## Changeset Rule

Releaseable SDK changes need a changeset:

```md
---
"Squad.SDK.NET": minor
---

Add preview validation for release candidates.
```

Use:

- `patch` for backwards-compatible fixes
- `minor` for backwards-compatible features
- `major` for breaking API changes

## Promotion Pipeline

- `dev -> preview`: merge dev, apply changesets, bump csproj version, update changelog, clear applied changesets
- `preview -> main`: merge validated preview into main
- `main`: creates `v<Version>` automatically and publishes the stable release
- `main -> dev`: the release commit is synced back into dev so new changesets start from the released baseline

## Anti-Patterns

- ❌ Branching from `main` for normal feature work
- ❌ PRs targeting `main` directly
- ❌ Pushing manual release tags
- ❌ Skipping `.changeset` files for releaseable SDK changes
- ❌ Leaving pending changesets on `preview` or `main`

---
> Source: [snickler/squad-sdk-net](https://github.com/snickler/squad-sdk-net) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
