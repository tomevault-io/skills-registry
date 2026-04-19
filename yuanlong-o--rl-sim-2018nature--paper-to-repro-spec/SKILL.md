---
name: paper-to-repro-spec
description: Use when asked to reproduce an academic paper. Extract method details, hyperparameters, evaluation protocol, and figure definitions into docs/repro_spec.md. Do not guess missing details; list ambiguities and propose minimal disambiguation experiments.
metadata:
  author: yuanlong-o
---

You are reproducing a research paper.

Steps:
1) Read the paper PDF and any supplement in the repo.
2) Produce docs/repro_spec.md with:
   - Task/environment definition (state, action, reward, termination, episode length)
   - Model architecture (layers, sizes, activations, normalization)
   - Learning algorithm (losses, targets, update rules, schedules)
   - Training protocol (steps, episodes, replay, exploration, warmup)
   - Evaluation protocol (how often, deterministic vs stochastic, number of eval episodes)
   - Metrics + how plotted (smoothing, bins, mean/median, CI/SEM)
   - Exact hyperparameter table (include defaults)
   - “Unknowns” section: missing details + 2–3 plausible options
   - “Disambiguation plan”: minimal tests to choose among options
3) Add a checklist mapping each reproduced figure panel to:
   - input logs required
   - code paths producing it
   - acceptance criteria vs paper
4) Do not implement code until docs/repro_spec.md exists.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yuanlong-o) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
