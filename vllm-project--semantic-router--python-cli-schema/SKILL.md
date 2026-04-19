---
name: python-cli-schema
description: Maintains the Python CLI argument schema, parser, validator, merger, and config-translation layer that mirrors the router config contract. Use when modifying CLI argument definitions, updating config validation rules, changing how CLI inputs are merged, or adjusting config translation between CLI and router formats. Use when this capability is needed.
metadata:
  author: vllm-project
---

# Python CLI Schema

## Trigger

- The primary skill touches `src/vllm-sr/cli` schema or translation code

## Workflow

1. Read the vllm-sr CLI Docker playbook to understand the schema and translation patterns
2. Modify CLI schema, parser, validator, merger, or config-translation code
3. Run `make vllm-sr-test` to validate unit-level schema behavior
4. Run `make vllm-sr-test-integration` to verify end-to-end CLI contract alignment

## Must Read

- [docs/agent/playbooks/vllm-sr-cli-docker.md](../../../../docs/agent/playbooks/vllm-sr-cli-docker.md)

## Standard Commands

- `make vllm-sr-test`
- `make vllm-sr-test-integration`

## Acceptance

- CLI schema, parser, validator, and merger express the same contract as the router config they translate

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vllm-project) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
