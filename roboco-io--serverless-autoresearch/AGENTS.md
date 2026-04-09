# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Purpose

Cost-effective reproduction of Karpathy's [autoresearch](https://github.com/karpathy/autoresearch) on AWS SageMaker Spot Training. Experiments are documented as tutorials. The upstream repo is at `upstream-autoresearch/` for reference.

## Architecture

A **parallel evolution pipeline** submits N train.py variants as simultaneous SageMaker Spot Training Jobs. Each generation: generate candidates → batch launch → collect results → select best → repeat.

```
train.py (root, agent-modified)
  → src/pipeline/candidate_generator.py creates N variants
  → src/pipeline/batch_launcher.py submits N SageMaker jobs (PyTorch DLC)
  → src/pipeline/result_collector.py polls CloudWatch for val_bpb
  → src/pipeline/selection.py picks best, git commits
  → src/pipeline/orchestrator.py loops the above
```

SageMaker jobs run `src/sagemaker/entry_point.py` → `src/sagemaker/train_wrapper.py` → `train.py` inside the container. Data/tokenizer are loaded from S3 input channels.

## Key Constraints (from autoresearch rules)

- **Only `train.py` is modifiable** — model architecture, optimizer, hyperparameters
- **`prepare.py` is read-only** — evaluation (`evaluate_bpb`), data loading, constants
- **No new dependencies** — only what's in `infrastructure/requirements-train.txt`
- **Fixed 5-minute training budget** (TIME_BUDGET=300s in prepare.py)
- **Metric: val_bpb** (validation bits per byte) — lower is better

## Commands

```bash
make dry-run       # Verify config, no AWS cost
make run-single    # Single experiment on SageMaker Spot
make run           # Full pipeline (10 gen × 10 pop)
make prepare       # One-time: download data + upload to S3
make cost          # Show SageMaker cost report
make setup         # Create IAM role
```

Underlying commands:
```bash
python -m src.pipeline.orchestrator --generations 10 --population 10
python -m src.pipeline.orchestrator --dry-run --single --population 3
python src/scripts/run_single.py
python src/scripts/prepare_s3.py
```

## Configuration

`config.yaml` (gitignored, copy from `config.yaml.example`):
- `aws.profile`, `aws.region`, `aws.role_arn` — AWS credentials
- `sagemaker.instance_type` — GPU instance (currently `ml.g7e.4xlarge`, L40S 48GB)
- `sagemaker.framework_version` — PyTorch DLC version (`2.8.0`)
- `pipeline.population_size` — candidates per generation
- `pipeline.num_generations` — evolution generations

## GPU Compatibility

train.py auto-detects GPU and selects attention backend:
- **Hopper (sm_90)**: Flash Attention 3 via `kernels` package
- **Ampere (sm_80/86)**: Flash Attention 3 via `kernels-community`
- **Ada Lovelace (sm_89, L40S)**: PyTorch SDPA fallback (FA3 not supported)

The `USE_FA3` flag and `FA3_SUPPORTED` check are at the top of train.py.

## Project Structure (Cookiecutter Data Science)

- `train.py`, `prepare.py` — Root (autoresearch convention)
- `src/pipeline/` — Evolution pipeline modules
- `src/sagemaker/` — SageMaker job entry point and wrapper
- `src/scripts/` — CLI utilities (prepare, run_single, cost_report)
- `infrastructure/` — Dockerfile, IAM setup, training requirements
- `experiments/` — Per-experiment reports (001-baseline, 002-optimization, etc.)
- `references/` — Research notes and external references
- `docs/` — Architecture diagrams (drawio/svg), comparison reports
- `notebooks/` — Jupyter analysis notebooks
- `generations/` — Pipeline output per generation (gitignored)

## Path Convention

All `src/` modules resolve project root via `Path(__file__).parent.parent.parent` (3 levels up from `src/pipeline/` or `src/scripts/`).

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for the full guide. Key rules:

- **Only `train.py` is modifiable** for experiments; `prepare.py` is read-only
- New experiments go in `experiments/NNN-description/` with README.md
- Run `make dry-run` before submitting PRs
- Track and report AWS costs in every PR
- Check [Spot Capacity Guide](docs/spot-capacity-guide.md) before choosing a region

## Current State

- **Working GPU**: ml.g7e.2xlarge (L40S, us-east-1, Spot quota=4)
- **Pending**: ml.p5.4xlarge (H100) quota approval for fair comparison with original
- **Best val_bpb**: 1.0643 on L40S with SDPA (vs 0.998 on H100 with FA3)
- **Total cost**: $0.44 for 25 experiments

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/roboco-io)
> Context snippets also available to append to your CLAUDE.md, GEMINI.md, and copilot-instructions.md — [download at TomeVault](https://tomevault.io/claim/roboco-io)
<!-- tomevault:4.0:agents_md:2026-04-08 -->
