---
name: 03-mofa-studio-dataflow
description: Dora dataflow authoring and wiring for MoFA Studio dynamic nodes. Use when editing voice-chat.yml, adding dynamic nodes, or debugging dataflow connections and signals. Use when this capability is needed.
metadata:
  author: mofa-org
---

# MoFA Studio Dataflow

## 1. Overview
MoFA apps rely on Dora dataflows with dynamic nodes. Follow naming and signal contracts so the bridge layer can discover and connect.

## 2. Dataflow workflow
1. Start from an existing `voice-chat.yml` (fm or debate).
2. Keep dynamic node IDs with `mofa-` prefix; add suffixes if needed.
3. Wire control and audio signals to `conference-controller`.
4. Ensure log outputs use `*_log` or `*_status` naming.
5. Validate with `dora up` and check UI connection status.

## 3. References
- references/dataflow-conventions.md
- references/signal-contracts.md
- references/dataflow-debugging.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mofa-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
