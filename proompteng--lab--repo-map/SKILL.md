---
name: repo-map
description: Navigate this repo quickly to find the correct app, package, or service, and identify the right files for changes. Use when this capability is needed.
metadata:
  author: proompteng
---

# Repo Map

## Overview

Use `rg` to search code and identify the correct area to edit. Check READMEs in relevant directories for commands.

## Structure

- `apps/`: Next.js/TanStack frontends
- `packages/`: shared TS libs + Convex backend
- `services/`: Go/Kotlin/Rails/Python services
- `argocd/`, `kubernetes/`, `tofu/`, `ansible/`: infra + GitOps
- `scripts/`, `packages/scripts/`: build/deploy helpers
- `skills/`: agent skills

## Find a service

```bash
rg -n "enrichRepository" services/bumba
rg --files -g "*bumba*"
```

## Find infra

```bash
rg -n "open-webui" argocd/applications
```

## Resources

- Reference: `references/repo-structure.md`
- Finder: `scripts/find-in-repo.sh`
- Notes: `assets/repo-notes.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/proompteng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
