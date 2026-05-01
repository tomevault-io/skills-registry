---
name: neuralink-decoder
description: Simulates and decodes neural spike activity into cursor movement (BCI). Use when this capability is needed.
metadata:
  author: openclaw
---

# Neuralink Decoder Skill

This skill simulates a Brain-Computer Interface (BCI).
It generates synthetic neural spiking data based on cosine tuning (motor cortex model) and uses a linear decoder to reconstruct cursor velocity.

## Features
- **Neural Simulator**: Generates realistic spike trains for 64 neurons.
- **Decoder**: Maps spike rates to 2D velocity ($v_x, v_y$).
- **Visualization**: Prints the decoded trajectory.

## Commands

- `decode`: Run the simulation and decoding loop.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
