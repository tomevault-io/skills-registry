---
name: agentuity-cli-cloud-eval-run-list
description: List eval runs. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Eval-run List

List eval runs

## Prerequisites

- Authenticated with `agentuity auth login`

## Usage

```bash
agentuity cloud eval-run list [options]
```

## Options

| Option | Type | Required | Default | Description |
|--------|------|----------|---------|-------------|
| `--count` | number | No | `10` | Number of eval runs to list (1–100) |
| `--projectId` | string | Yes | - | Filter by project ID |
| `--all` | boolean | Yes | - | List all eval runs regardless of project context |
| `--evalId` | string | Yes | - | Filter by eval ID |
| `--agentId` | string | Yes | - | Filter by agent ID |
| `--sessionId` | string | Yes | - | Filter by session ID |

## Examples

List 10 most recent eval runs:

```bash
bunx @agentuity/cli cloud eval-run list
```

List 25 most recent eval runs:

```bash
bunx @agentuity/cli cloud eval-run list --count=25
```

Filter by eval:

```bash
bunx @agentuity/cli cloud eval-run list --eval-id=eval_*
```

Filter by session:

```bash
bunx @agentuity/cli cloud eval-run list --session-id=sess_*
```

Filter by project:

```bash
bunx @agentuity/cli cloud eval-run list --project-id=proj_*
```

Filter by agent:

```bash
bunx @agentuity/cli cloud eval-run list --agent-id=agent_*
```

List all eval runs regardless of project context:

```bash
bunx @agentuity/cli cloud eval-run list --all
```

## Output

Returns: `array`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
