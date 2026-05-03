---
name: trm-interp
description: Standardize mechanistic interpretability experiments for TRM-style recurrent models with a fixed trace to SAE feature to intervention to report loop. Use when implementing or running reproducible tomography studies, feature ablation and patch tests, and baseline versus intervened evaluation reports from stepwise traces. Use when this capability is needed.
metadata:
  author: patrickdugan
---

# TRMInterp

Use this workflow to prevent one-off experiment scripts and keep outputs comparable.

## Contract

Enforce exactly three commands:

1. `trminterp trace`
2. `trminterp sae fit`
3. `trminterp intervene`

Enforce exactly two report formats:

1. `trace.jsonl`
2. `report.json`

Allow optional caches/artifacts:

- `states.npy`
- `sae.pt`
- `sae_metrics.json`
- intervention diffs JSON under the same run folder

## Loop

Run in this order:

1. `trminterp trace`
2. `trminterp sae fit`
3. `trminterp intervene`

Use deterministic seeds for every stage.
Write all generated outputs under a single run directory.
Never mix code changes and generated outputs in the same commit unless explicitly requested.

## Command Mapping

If the repo has no `trminterp` binary yet, map to existing modules and keep output names stable:

1. `trminterp trace`:
`python -m mechinterp_cli trace --out-dir <run_dir>/traces --seed <seed>`
Then normalize/rename the selected trace file to `<run_dir>/trace.jsonl`. Optionally export stacked states to `<run_dir>/states.npy`.

2. `trminterp sae fit`:
`python -m analysis.sae --summary-json <run_dir>/summary.json --out-json <run_dir>/sae_metrics.json --save-model-json <run_dir>/sae.pt`

3. `trminterp intervene`:
`python -m analysis.causality --summary-json <run_dir>/summary.json --seed <seed> --out-json <run_dir>/report.json`

Keep the report focused on baseline vs intervened outcomes and top causal features.

## Intervention Policy

Implement only these policies in v0:

1. `ablate_features`
2. `clamp_features`
3. `patch_features`

Avoid GUI work, feature browsers, seed-matching research tooling, and model-zoo abstractions in this skill version.

## Internal API

Keep internal abstractions minimal:

1. `TraceDataset.load(path) -> states[N,D], meta`
2. `SAE.encode(h) -> a`, `SAE.decode(a) -> h_hat`
3. `InterventionPolicy.apply(step, h, sae, context) -> h_prime`
4. `Evaluator.compare(baseline_trace, new_trace) -> report`

Read `references/v0-spec.md` for the exact payload expectations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/patrickdugan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
