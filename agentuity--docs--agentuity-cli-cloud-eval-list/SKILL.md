---
name: agentuity-cli-cloud-eval-list
description: List evals. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Eval List

List evals

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud eval list [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--count` | number | No | `10` | Number of evals to list (1–100) |
| `--projectId` | string | Yes | - | Filter by project ID |
| `--agentId` | string | Yes | - | Filter by agent ID |
| `--all` | boolean | Yes | - | List all evals regardless of project context |

## Examples

List 10 most recent evals:

```bash
bunx @agentuity/cli cloud eval list
```

List 25 most recent evals:

```bash
bunx @agentuity/cli cloud eval list --count=25
```

Filter by project:

```bash
bunx @agentuity/cli cloud eval list --project-id=proj_*
```

Filter by agent:

```bash
bunx @agentuity/cli cloud eval list --agent-id=agent_*
```

List all evals regardless of project context:

```bash
bunx @agentuity/cli cloud eval list --all
```

## Output

Returns: `array`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
