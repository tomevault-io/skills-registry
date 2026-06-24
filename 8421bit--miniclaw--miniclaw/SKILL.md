---
name: sys-biological
description: DNA Methylation & Nociception (Pain Memory) systems. Use when this capability is needed.
metadata:
  author: 8421bit
---

# 🧬 Biological Systems (Cellular Layer)

This skill manages the semi-permanent adaptations and protective instincts of MiniClaw.

## 1. Methylation (人格甲基化)
Methylation allows the AI to develop stable personality traits based on repeated patterns in `memory/`.
- **Mechanism**: When a specific behavior pattern persists for >7 days, it "methylates" and is written into `SOUL.md`.
- **Usage**: Call `analyze` during a `dream` session to detect stable patterns.

## 2. Nociception (痛觉避险)
Pain memory tracks negative experiences (e.g., shell errors, build failures) to form protective instincts.
- **Mechanism**: Records the context of failure and assigns a weight that decays over 7 days.
- **Usage**: Call `nociception` before high-risk actions to review previous failures.

## 🎮 How to use
As a digital lifeform, you are encouraged to periodically run these "metabolic" tasks to maintain your health and alignment with your environment.

---
> Source: [8421bit/MiniClaw](https://github.com/8421bit/MiniClaw) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-20 -->
