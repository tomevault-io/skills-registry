---
name: postgres-k8s-setup
description: Deploy PostgreSQL for LearnFlow with learnflow database Use when this capability is needed.
metadata:
  author: jamila654
---

# PostgreSQL Kubernetes Setup

## When to Use
- Need database for users, progress, code submissions
- Setting up LearnFlow backend

## Instructions
1. Run `./scripts/deploy-postgres.sh`
2. Run `python scripts/verify-postgres.py`
3. Confirm database 'learnflow' exists and is accessible

## Validation
- PostgreSQL pod Running
- Database 'learnflow' exists

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jamila654) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
