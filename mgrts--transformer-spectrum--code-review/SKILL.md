---
name: code-review
description: Review pending changes in the transformer-spectrum repo for correctness and the silent-bug classes this spectral-analysis research codebase actually hits — SGT/robust-loss math & gradients, spectral-metric reliability (HTSR vs Zipf, small-matrix Hill/KS, Marchenko-Pastur), the paired-seed/leakage/test-split experiment protocol, the ES/GA tiny-model + matched-loss contract, the MLflow metric-name & artifact-selector contract, model tensor-shape/autoregressive/checkpoint contracts, and secrets/large-file hygiene. Read-only by default; surfaces findings grouped by severity. Use before every commit, or via /commit-push. Use when this capability is needed.
metadata:
  author: mgrts
---

# Code review for transformer-spectrum

Review the changes currently in the working tree (staged + unstaged + untracked)
against the standards that matter for this codebase specifically. Most bugs here are
**silent**: they pass `pytest` (the 93 tests assert shapes + isfinite, mock nothing
heavy, and never exercise the sweep/experiment paths) yet change the loss math, the
spectral metric definitions, the experiment's scientific validity, or the logged
metric names. The job is to catch those.

The review is **read-only by default** — fixes are surfaced as recommendations and
only applied if the user explicitly asks.

## Arguments

`$ARGUMENTS` — optional. Specific files or globs to scope the review (defaults to the
entire diff).

## Flow

### Step 1: Gather changes

```bash
git status --short
git diff --staged --stat
git diff --stat
```

If there is nothing pending, stop: "Nothing to review."

### Step 2: Read the diff

For each changed file, read the actual diff (not just the file list). Note which
subsystems are touched — that selects which checks below apply and which subagent to
delegate to.

### Step 3: Delegate deep audits to subagents

When the diff touches a fragile subsystem, dispatch the matching subagent (via the
Agent tool, `subagent_type`) and fold its findings into the report. Run independent
subagents in parallel.

- Touches `modeling/loss_functions.py` or `settings.py` `SGTLossConfig`
  → **`loss-math-reviewer`**.
- Touches `metrics/spectral_metrics.py`, `metrics/heavy_tail_estimation.py`, or
  `modeling/trainer.py` `SpectralLoggingMixin` → **`spectral-metrics-reviewer`**.
- Touches `experiments/*.py`, `data/**/*.py`, `modeling/data_processing.py`,
  `modeling/harness.py`, `train.py`/`train_es.py`/`train_ga.py`, or the seed/split/ES/GA
  paths in `trainer.py` → **`experiment-methodology-auditor`**.
- Touches `experiments/utils/collect_artifacts.py`, the MLflow `log_metric(s)` calls in
  `trainer.py`/`train*.py`, `models.py` (`cached_mask`), or the `ESConfig` definition
  → **`mlflow-contract-auditor`**.

For a small diff that clearly matches none of these, do the checks inline.

### Step 4: CRITICAL — Loss math & gradient correctness

Applies to `modeling/loss_functions.py` and `settings.py` `SGTLossConfig`.

- **SGT moment-domain guard:** `SGTLoss.__init__` keeps `if not (p * q > 2): raise` —
  this is load-bearing (the beta functions `beta(2/p, q-1/p)`, `beta(3/p, q-2/p)` need
  `q - 2/p > 0`, else silent NaN/degenerate loss). `SGTLossConfig` carries the same
  `model_validator` (`p*q > 2`). Removing either reopens the NaN-loss bug.
- **SGT residual & skew:** `forward` keeps `diff = y_true - y_pred + m`; the skew term
  `(1 + lam*torch.sign(diff))**p` reads that same `diff`. The trainer calls
  `criterion(out, tgt)` (pred first, truth second).
- **eps guards & log1p:** SGT keeps its `+ eps` denominators and `torch.log1p(ratio)`;
  Cauchy keeps `gamma * torch.log1p(diffs**2 / gamma)`.
- **dtype/device:** every `torch.tensor` constant in `SGTLoss` derives `dtype=`/`device=`
  from the input; the loss returns a scalar `.mean()`.
- **Factory:** `get_loss_function` still maps `mse→MSELoss`, `mae→L1Loss`,
  `cauchy→CauchyLoss(gamma=2.0 default)`, `sgt→SGTLoss(p=2.0, ...)`; the default
  `TRAINING_CONFIGS` SGT entries all satisfy `p*q > 2` (q ≥ 1.001 at p=2).

### Step 5: CRITICAL — Spectral-metric methodology

Applies to `metrics/spectral_metrics.py`, `metrics/heavy_tail_estimation.py`,
`modeling/trainer.py` `SpectralLoggingMixin`.

- **Naming honesty:** `zipf_sv_slope` is a rank/Zipf slope, NOT the Martin-Mahoney HTSR
  tail index. Reject any rename back to `alpha_exponent` or any code/doc that equates it
  with `pl_alpha_hill`/`pl_alpha_ks`.
- **Small-matrix guard:** `_hill_alpha`/`_ks_alpha` keep the `MIN_TAIL_POINTS` guard
  (return NaN when the tail is too small). The eigenvalue count is `min(rows, cols) =
  embed_dim` for EVERY matrix (incl. FFN) — do not claim FFN gives more eigenvalues.
- **MP baseline:** `get_spectral_metrics` still returns `mp_lambda_plus`, `mp_n_spikes`,
  `mp_signal_energy_frac` and computes the SVD **once** (passes `s` into the helpers).
- **Matrix selection:** `_ATTN_SPECS` covers encoder self-attn, decoder self-attn AND
  decoder cross-attn; `_FFN_SPECS` covers linear1/linear2. Don't silently narrow it.
- **kappa reproducibility:** `estimate_kappa_exponent` keeps the explicit
  `np.random.default_rng(random_state)` + bootstrap averaging + the `M_1/M_n > 0` guard;
  `compute_dispersion_scaling_series` starts at `n=2`.

### Step 6: CRITICAL — Experiment protocol & data methodology

Applies to `experiments/*.py`, `data/**`, `modeling/data_processing.py`,
`modeling/harness.py`, `train*.py`.

- **Paired seeds:** `experiments/{synthetic,owid_covid,rvr_us}_data.py` derive one
  deterministic seed per run (`base_seed + run_idx`) shared across ALL losses in that
  run (paired init + split). Reject any reintroduction of unseeded `random.randint`, or
  a loop that gives different losses different seeds within a run.
- **No leakage:** the per-chunk `StandardScaler` in `data/rvr_us/dataset.py` and
  `data/owid_covid/dataset.py` is fit on the INPUT window only (`chunk[:input_len]`),
  never the full window that includes the forecast horizon.
- **Test set:** the harness builds train/val/TEST; val selects the best model, `test_*`
  metrics are reported. Reject reporting selection metrics as final results.
- **ES/GA tiny model:** `train_es.py`/`train_ga.py` (and the CLI) default to the tiny
  model (`embed_dim=16, num_heads=2, num_layers=1, dim_feedforward=32`, ~6k params) —
  black-box opt cannot train the full 2.3M-param net. GA `init_lo/hi` stay near the
  PyTorch init scale (~±0.15, not ±0.5); `TrainerGA._make_objective` uses
  `resample_each_call=False` (fixed batch set). Any cross-optimizer spectral claim must
  be at MATCHED final train loss.
- **Smoothing:** `generate_data.main` keeps the excess-kurtosis warning (smoothing
  attenuates the heavy tail). Matched epochs: sweeps disable early stopping.

### Step 7: CRITICAL — MLflow contract, ESConfig, checkpoint portability

Applies to `experiments/utils/collect_artifacts.py`, `trainer.py`/`train*.py` logging,
`models.py`, the `ESConfig` definition.

- **Artifact selectors:** `collect_artifacts._download_artifacts` selectors
  (`esd_*.json`, `singular_vals_*.json`) match the names `log_qkv_figs` actually dumps.
- **Metric names:** training logs `*_mae`/`*_rmse`/`*_wmape` (NOT `*_mape`/`*_smape`),
  the `mp_*`/`zipf_sv_slope`/`pl_alpha_*`/`spectral_entropy`/`stable_rank` spectral keys,
  and `test_*`. A rename must update every consumer (`collect_artifacts`, notebooks, the
  `aggregate.py` `--metric` default).
- **Single ESConfig:** there is ONE `ESConfig` (pydantic, in `settings.py`); `trainer.py`
  re-exports it. Reject a second dataclass `ESConfig`.
- **Checkpoint portability:** `TransformerWithPE` keeps `register_buffer('cached_mask',
  None, persistent=False)` so a fresh model can `load_state_dict(strict=True)`.
- **evotorch optional:** evotorch is imported lazily inside `TrainerGA.fit` (not at
  module top); `requests` stays declared in `pyproject.toml`.

### Step 8: HIGH — Model tensor-shape & autoregressive contracts

- `forward()`/`infer()` return `(B, tgt_len, out_dim)`, keep `batch_first=True`, build
  and pass the causal `tgt_mask` (`.to(tgt.device)`); dropping it leaks future tokens.
- `PositionalEncoding.forward` keeps its length guard; `TransformerWithPE` threads
  `dim_feedforward`. `LSTM.__init__` keeps the `input_dim == output_dim` guard.
- Any new model in the `get_model` factory implements BOTH `forward(src, tgt)` and
  `infer(src, tgt_len)` (else `log_sample_images` crashes after a run).

### Step 9: MEDIUM — Style, hygiene & docs drift

- Line length **99** consistent across `pyproject.toml` (`[tool.black]`, `[tool.isort]`,
  `[tool.ruff]`) and `setup.cfg [flake8]`. `.pre-commit-config.yaml` pins a modern
  `black` (not `20.8b1`). `make format` then `pre-commit run --all-files` yield no diff.
- Package version stays under `[project]` in `pyproject.toml` (flit backend; no
  `[tool.poetry]`). `requests` declared.
- **No secrets / no artifact files**: nothing staged under `data/`, `models/`,
  `mlruns/`, no `*.npy`/`*.pt`/`*.pkl`/`*.ipynb`, no `git add -f`, no file `> 10 MB`.
  (The `block_large_secret` hook also guards this.)
- New public function/class has a docstring + type hints; tensor shapes documented in
  `forward`/`infer` stay in sync. New source module gets a matching `test_<module>.py`.
- If tests/metrics/CLI changed, README + CLAUDE.md (test count, metric names, CLI
  reference, invariants) were updated.

### Step 10: Run tests + hooks

Run both and report exit status:

```bash
make test                      # pytest (currently 93 tests)
pre-commit run --all-files     # or: make pre-commit
```

Either failing is a critical finding. (Formatting hooks auto-fix on a re-run; report
what changed.)

### Step 11: Report findings

Group by severity:

- **Critical** — wrong loss math/NaN gradient, spectral-metric definition/guard break
  (HTSR-vs-Zipf relabel, dropped Hill/KS guard, lost MP baseline), paired-seed/leakage/
  test-split regression, ES/GA tiny-model or matched-loss break, MLflow metric-name or
  artifact-selector break, duplicate ESConfig, `cached_mask` portability break, broken
  test/pre-commit, secret/large-file leak.
- **High** — model shape/autoregressive/checkpoint contract break, evotorch hard import,
  a known silent-bug-class match.
- **Medium** — style/hygiene/version-table violation, docs drift.
- **Low** — comment / naming / docstring / type-hint polish.

For each finding: file path, the symbol or line, and a concrete suggestion. Do not make
changes unless the user asks.

If there are zero findings: report "Review passed — N files reviewed, M lines changed,
pytest <result>, pre-commit <result>."

---
> Source: [mgrts/transformer-spectrum](https://github.com/mgrts/transformer-spectrum) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
