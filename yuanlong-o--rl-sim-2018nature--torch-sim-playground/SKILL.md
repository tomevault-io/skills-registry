---
name: torch-sim-playground
description: Use when implementing an RL simulation playground in PyTorch. Create clean interfaces for env, agent, training loop, logging, and evaluation with deterministic seeding support.
metadata:
  author: yuanlong-o
---

Implement a minimal, testable RL playground in PyTorch.

Requirements:
- Define src/envs/ with a base Env interface:
  reset(seed) -> obs
  step(action) -> obs, reward, terminated, truncated, info
- Define src/agents/ with:
  - model.py (torch.nn.Module)
  - policy.py (action selection, exploration)
  - learner.py (update step)
- Define src/train.py and src/eval.py as pure functions that take a config dict.
- Add scripts/train.py entrypoint:
  - loads config
  - sets seeds
  - runs training
  - writes logs to results/<run_id>/
- Add scripts/eval.py and scripts/make_figures.py placeholders.
- Add a tiny smoke test:
  - run 100 steps on CPU, ensure no crash, logs created.

Logging:
- Save config.json
- Save metrics.jsonl or metrics.csv
- Save summary.json with final metrics

Determinism:
- Provide a single function set_seed(seed) used everywhere.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yuanlong-o) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
