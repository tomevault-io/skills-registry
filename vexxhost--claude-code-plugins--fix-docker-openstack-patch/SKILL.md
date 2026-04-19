---
name: fix-docker-openstack-patch
description: Fix a failing patch in a Docker OpenStack image repository. Use when a Renovate PR has patch conflicts. Use when this capability is needed.
metadata:
  author: vexxhost
---

## Context

Docker OpenStack repositories use patches in `patches/` to modify upstream code. The commit being checked out can be found in `.github/workflows/build.yml`. Patches may fail to apply because:

1. **Patch merged upstream** - delete the patch
2. **Upstream breaking change** - update the patch to apply cleanly

## Workflow

Use `gh` CLI for all GitHub operations.

1. `gh pr checkout` the PR
2. `gh run view` to find which patch failed
3. Determine if patch is merged upstream or needs updating:
   - Do not assume a patch is merged upstream - verify by checking the upstream code
   - Confirm the upstream changes match the intent and content of the old patch
4. Apply the fix:
   - Merged upstream: delete the patch
   - Needs updating: create new patch preserving original intent
5. Use `git:commit-message` skill to generate commit message, commit, and `git push`
6. `gh run watch` until build passes (repeat if needed)
7. `gh pr merge` once the build passes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vexxhost) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
