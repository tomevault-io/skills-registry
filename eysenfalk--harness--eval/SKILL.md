---
name: eval
description: Run the eval framework to measure agent output quality. Use when this capability is needed.
metadata:
  author: eysenfalk
---

# /eval — Run Agent Quality Evals

Execute eval scenarios to measure agent output quality mechanically.

## What It Does

1. Reads scenario definitions from `evals/scenarios/`
2. For each scenario: sets up workspace, runs agent, checks output
3. Scores: files exist, content matches, forbidden patterns absent, commands pass
4. Saves results to `evals/results/` as timestamped JSON
5. Prints summary scorecard

## Usage

Run all scenarios:

```bash
./scripts/eval.sh
```

Run a specific scenario:

```bash
./scripts/eval.sh evals/scenarios/$ARGUMENTS
```

## After Running

1. Check `evals/results/latest.json` for detailed results
2. If any scenario failed, investigate which checks failed
3. Use failures to improve CLAUDE.md instructions or pre-commit hooks
4. Track trends: are agents getting better over time?

## Adding New Scenarios

Create a YAML file in `evals/scenarios/` following the format in `evals/README.md`.

Each scenario needs:

- `name`: what we're testing
- `setup`: commands to prepare the workspace
- `prompt`: what to tell the agent
- `checks`: mechanical verification of output

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eysenfalk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
