---
name: target-brief-baseline-stage
description: Single-stage acceptance playbook for the baseline context phase. Use when this capability is needed.
metadata:
  author: understudy-ai
---

# target-brief-baseline-stage

## Goal

Run the baseline context stage end to end for a selected target.

## Inputs

- targetName

## Child Artifacts

- collect-target-context

## Stage Plan

1. [skill] collect-target-context -> Collect baseline target context | inputs: targetName, artifactsRootDir | outputs: target.json, context.md | retry: retry_once
2. [approval] baseline-preview -> Wait for human approval | outputs: approval.state | approval: baseline_review

## Output Contract

- target.json
- context.md

## Approval Gates

- Human review before continuing beyond the baseline stage

## Failure Policy

- Keep the partial context output if the stage cannot finish in one pass

---
> Source: [understudy-ai/understudy](https://github.com/understudy-ai/understudy) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-01 -->
