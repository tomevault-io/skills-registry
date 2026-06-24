---
name: train-model
description: | Use when this capability is needed.
metadata:
  author: zelk-oss
---

# What this skill does

This skill is responsible for:

- Running the training entrypoint `scripts/train.py` from the repository root.
- Ensuring the run happens inside the `template_beginner` conda environment.
- Monitoring the training subprocess and periodically reporting whether it is still running.

It does **not** try to interpret or modify model configs; it simply runs the training
command the user provides (or a sensible default) and watches it to completion.

# When to use

Use this skill when the user wants to:

- Run model training for an experiment (e.g., `python scripts/train.py model=era5 ...`).
- Start a new training run and see live progress until completion.
- Confirm a given training command completes successfully (including early failures).

# How to run

1. **Ensure you are at the repository root.**
2. Run training using the `template_beginner` conda environment.

The canonical command is:

```bash
cd code
conda run -n template_beginner --no-capture-output python scripts/train.py <hydra overrides...>
```

> Note: `conda run` avoids needing to `source activate` and works in non-interactive shells.

## Monitoring progress

To satisfy the "repeatedly check if… still running" requirement, run the training command
under a small monitor loop that prints a heartbeat every few seconds while the process is alive.

This skill reuses the `run-python` skill’s monitor helper located at
`./.claude/skills/run-python/scripts/monitor_python.py`.

Example usage (from the repo root):

```bash
python .claude/skills/run-python/scripts/monitor_python.py \
  --env template_beginner \
  --cwd code \
  --check-interval 10 \
  -- python scripts/train.py model=shallow_water +experiments=shallow_water/det_mse n_epochs=1 device=cpu
```

This will:
- activate the `template_beginner` environment via `conda run`
- start the training process in ``
- print a heartbeat line every 10 seconds until training exits
- propagate the training process exit code

# Example (shallow water smoke test)

A minimal smoke test for the shallow water model (fast, single epoch):

```bash
python .claude/skills/run-python/scripts/monitor_python.py \
  --env template_beginner \
  --cwd code \
  --check-interval 10 \
  -- python scripts/train.py model=shallow_water +experiments=shallow_water/det_mse n_epochs=1 device=cpu
```

# Tips

- If `conda run` fails, confirm the `template_beginner` environment exists (see `environment.yml`).
- If training is hanging, inspect the latest log output for exceptions and ensure the dataset files are available.
- You can adjust `--check-interval` to print status more or less frequently.

---
> Source: [zelk-oss/template_beginner](https://github.com/zelk-oss/template_beginner) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
