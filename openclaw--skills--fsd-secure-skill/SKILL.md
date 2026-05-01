---
name: fsd-secure
description: Full Self-Driving agent with highest safety standards (Camera-Only, Redundant Checks). Use when this capability is needed.
metadata:
  author: openclaw
---

# FSD Secure Skill

This skill implements a **Camera-Only Full Self-Driving** agent designed for maximum safety.
It runs in a simulated environment and uses **Dual-Pass Analysis** to verify clear paths.

## Safety Features
- **Dual-Pass Verification**: Two independent algorithms must agree the path is clear.
- **Temporal Consistency**: Requires 3 consecutive safe frames before acceleration.
- **Fail-Safe**: Any uncertainty triggers an immediate Emergency Stop.

## Commands

- `drive`: Start the autonomous driving simulation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
