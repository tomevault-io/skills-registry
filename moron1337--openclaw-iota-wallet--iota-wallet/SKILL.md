---
name: iota-wallet
description: Operate IOTA wallet tools through a safe approval-gated flow in OpenClaw. Use when this capability is needed.
metadata:
  author: moron1337
---

# IOTA Wallet Skill

Use this flow for every state-changing operation:

1. Run `iota_prepare_transfer`.
2. If approvals are enabled, run `iota_approve_transfer`.
3. Run `iota_execute_transfer`.

Rules:

- Prefer read-only tools (`iota_active_env`, `iota_get_balance`, `iota_get_gas`) for diagnostics.
- Never bypass recipient allowlist or transfer amount policy.
- If an execution tool reports `not_implemented`, stop and report which milestone is required.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/moron1337) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
