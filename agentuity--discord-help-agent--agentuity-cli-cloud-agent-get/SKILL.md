---
name: agentuity-cli-cloud-agent-get
description: Get details about a specific agent. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Agent Get

Get details about a specific agent

## Prerequisites

- Authenticated with `agentuity auth login`
- Project context required (run from project directory or use `--project-id`)

## Usage

```bash
agentuity cloud agent get <agent_id>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<agent_id>` | string | Yes | - |

## Examples

Get item details:

```bash
bunx @agentuity/cli cloud agent get agent_abc123
```

Show output in JSON format:

```bash
bunx @agentuity/cli --json cloud agent get agent_abc123
```

## Output

Returns JSON object:

```json
{
  "id": "string",
  "name": "string",
  "description": "unknown",
  "identifier": "string",
  "deploymentId": "unknown",
  "devmode": "boolean",
  "metadata": "unknown",
  "createdAt": "string",
  "updatedAt": "string",
  "evals": "array"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | - |
| `name` | string | - |
| `description` | unknown | - |
| `identifier` | string | - |
| `deploymentId` | unknown | - |
| `devmode` | boolean | - |
| `metadata` | unknown | - |
| `createdAt` | string | - |
| `updatedAt` | string | - |
| `evals` | array | - |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
