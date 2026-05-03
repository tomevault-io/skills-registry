---
name: unify
description: > Use when this capability is needed.
metadata:
  author: zpankz
---

# Unify

`unify` converts a directory into a minimal progressive architecture:
`extract → cluster → decompose → bridge → optimize`.

## Contract

- Input: filesystem directory.
- Output: deterministic artifacts via `--output`.
- Order: `core → cluster_base → specific → bridge`.
- Failures are explicit via exit code + stderr.

## Invocation

```bash
python scripts/unify.py <target_dir> --output ./unified
python scripts/unify.py <target_dir> --analyze-only
python scripts/unify.py <target_dir> --threshold 0.95 --max-iter 80
```

| Arg | Default | Purpose |
| --- | --- | --- |
| target | required | Directory to ingest |
| `--output` | `./unified` | Destination for artifacts |
| `--threshold` | `0.95` | Target score convergence |
| `--max-iter` | `50` | Optimization budget |
| `--max-clusters` | `12` | Maximum clusters |
| `--block-size` | `500` | Max lines per specific module |
| `--chunk-size` | `4096` | Byte chunk size for large files |
| `--analyze-only` | false | Emit analysis only |
| `--verbose` | false | Print phase telemetry |

## Outputs

- `index.md`: progressive manifest
- `analysis.json`: graph/cluster/bridge metadata
- `report.md`: score and diagnostics
- `core/`, `modules/`, `bridges/`: emitted module tiers

## Optimization model

Objective (9): parsimony, redundancy, connectivity, DAG validity,
bridge coverage, load efficiency, modularity, depth ratio, convergence.

## Deterministic failure/recovery
- Empty input: exit `1` + parse error.
- Weak signal: rerun `--analyze-only`, `+max-clusters`, or `-threshold`.
- Cycle churn: `+max-iter`, `-block-size`.

## Runtime boundaries

- No source execution.
- Binary assets skipped.
- Excludes `.git`, `node_modules`, `venv`, `dist`, `build`.
- Fixed defaults + fixed snapshot => deterministic output.
- Runtime dependency: `networkx>=3.0`.

## Orchestration usage

Downstream: `graph`, `hierarchical-reasoning`, `ontolog`, `critique`.
`unify` emits portable Markdown/JSON contracts.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zpankz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
