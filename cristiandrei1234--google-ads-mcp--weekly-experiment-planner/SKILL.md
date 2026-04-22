---
name: weekly-experiment-planner
description: Weekly experimentation planning and lifecycle management. Use when selecting, scheduling, and tracking Google Ads tests. Use when this capability is needed.
metadata:
  author: cristiandrei1234
---

# weekly-experiment-planner

## Cadence
Weekly

## Objective
Maintain a steady pipeline of high-value experiments with clean measurement.

## Workflow
- Build hypothesis backlog from recent anomalies and opportunities.
- Define test design: control, treatment, duration, success metric, stop conditions.
- Create or update experiment entities and schedule execution when approved.
- Report experiment status, risks, and next decision date.

## Core MCP Tools
- list_experiments
- create_experiment
- get_experiment
- update_experiment
- schedule_experiment
- end_experiment
- promote_experiment
- list_experiment_arms

## Expected Outputs
- Experiment backlog
- Active experiment board
- Decision-ready status report

## Guardrails
- Do not overlap conflicting experiments on same inventory.
- Require measurable success criteria before launch.
- Avoid budget shocks during experiment windows.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cristiandrei1234) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
