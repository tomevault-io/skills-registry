---
name: optical-quantum-kernel
description: Simulates a quantum kernel using optical fiber storage and linear optics. Use when this capability is needed.
metadata:
  author: openclaw
---

# Optical Quantum Kernel Skill

This skill simulates a photonic quantum computer that uses optical fibers for storage and linear optics for computation.
It calculates the quantum kernel (similarity) between two data vectors by encoding them into optical phases, passing them through simulated fibers (with loss), and interfering them.

## Security Features
- **Resource Bounding**: Capped at 8 modes to prevent resource exhaustion.
- **Input Validation**: Strict checks on input vector dimensions and limits.
- **Physics-Based Constraints**: Includes attenuation and phase noise for realism.

## Commands

- `simulate`: Run the quantum kernel simulation on two input vectors.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
