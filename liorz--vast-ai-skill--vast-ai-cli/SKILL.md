---
name: vast-ai-cli
description: Vast.ai CLI workflows for searching GPU offers, creating and managing instances, handling volumes and data transfer, and troubleshooting authentication or command errors. Use when Codex needs to run or compose vastai/vast.py commands, interpret results, or automate Vast.ai marketplace tasks. Use when this capability is needed.
metadata:
  author: liorz
---

# Vast.ai CLI

## Overview

Use this skill to translate user requests into correct Vast.ai CLI commands, explain outputs, and handle common workflows (search, provision, manage, transfer, and cleanup).

## Quick Start Workflow

1) Confirm CLI + auth
- Prefer `vastai` if installed; otherwise use `python vast.py` or `./vast.py` from the repo.
- Ensure an API key is set (`vastai set api-key ...` or `VAST_API_KEY`).

2) Search offers
- Build a filter string and optional ordering/limit.
- Use `--raw` when machine-readable output is needed.

3) Create or launch an instance
- Use `create instance OFFER_ID ...` for a fresh instance.
- Use `launch instance OFFER_ID --template_hash ...` when starting from a template.

4) Operate instances
- `show`, `start`, `stop`, `reboot`, `destroy` as needed.
- Ask before destructive operations (destroy, delete volumes).

5) Transfer data
- Use `vastai copy` for file transfer; use `vastai ssh-url` to obtain SSH access.

6) Clean up
- Stop or destroy instances and remove unused volumes.

## Task Guidance

### Search and pricing
- Compose filters using `field operator value` (see references for fields/operators).
- Prefer clear constraints: GPU model, RAM, reliability, cost per hour.
- Use `-o` to order results (ascending `+`, descending `-`).

### Instance creation
- Always include image + storage requirements.
- For notebooks or services, include `--jupyter` or port mappings.
- When asking for spot/bid pricing, use `--bid_price`.

### Data transfer
- For large transfers, prefer `vastai copy` over ad-hoc scp since it uses instance metadata.

### Troubleshooting
- Auth errors: confirm API key and config path.
- Rate limits: retry or add backoff; avoid rapid tab-completion queries.
- Missing command: check `vastai --help` or `python vast.py --help`.

## References

- Read `references/vast-cli.md` for command examples, search fields, and global options.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/liorz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
