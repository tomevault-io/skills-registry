---
name: deploy-prod
description: Start the production Docker stack (API + Web + DB + Redis) Use when this capability is needed.
metadata:
  author: sjdodge123
---

# Deploy Production Stack

Start the full production Docker stack using the deploy script.

Run: `./scripts/deploy_prod.sh $ARGUMENTS`

Common flags:
- `(no args)` — Start Docker stack with cached images
- `--rebuild` — Rebuild images then start
- `--down` — Stop all containers
- `--status` — Show container status
- `--logs` — Tail API logs

The production stack runs on http://localhost:80.

After startup, display the admin credentials and available URLs from the script output.

---
> Source: [sjdodge123/Raid-Ledger](https://github.com/sjdodge123/Raid-Ledger) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
