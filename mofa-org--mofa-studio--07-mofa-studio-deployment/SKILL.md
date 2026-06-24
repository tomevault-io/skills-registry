---
name: 07-mofa-studio-deployment
description: Run and deploy MoFA Studio via Nix or manual builds, including dataflow startup and environment variables. Use when launching the app or preparing a dev environment. Use when this capability is needed.
metadata:
  author: mofa-org
---

# MoFA Studio Deployment

## 1. Overview
Use Nix for the full dependency chain. Manual mode is possible but requires Python nodes and models.

## 2. Deployment workflow
1. Choose Nix or manual run.
2. Ensure dataflow directory exists for the target app.
3. Start dora daemon and dataflow.
4. Launch the GUI.

## 3. References
- references/nix-run.md
- references/manual-run.md
- references/deploy-edge-cases.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mofa-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
