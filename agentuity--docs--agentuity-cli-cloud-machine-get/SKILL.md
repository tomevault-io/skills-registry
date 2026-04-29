---
name: agentuity-cli-cloud-machine-get
description: Get details about a specific organization managed machine. Requires authentication. Use for Agentuity cloud platform operations Use when this capability is needed.
metadata:
  author: agentuity
---

# Cloud Machine Get

Get details about a specific organization managed machine

## Prerequisites

- Authenticated with `agentuity auth login`
- Organization context required (`--org-id` or default org)

## Usage

```bash
agentuity cloud machine get <machine_id>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `<machine_id>` | string | Yes | - |

## Examples

Get machine details by ID:

```bash
bunx @agentuity/cli cloud machine get machine_abc123xyz
```

## Output

Returns JSON object:

```json
{
  "id": "string",
  "status": "string",
  "provider": "string",
  "region": "string",
  "instanceId": "unknown",
  "privateIPv4": "unknown",
  "availabilityZone": "unknown",
  "deploymentCount": "number",
  "orgId": "unknown",
  "orgName": "unknown",
  "createdAt": "string",
  "updatedAt": "unknown",
  "startedAt": "unknown",
  "stoppedAt": "unknown",
  "error": "unknown"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Machine ID |
| `status` | string | Machine status |
| `provider` | string | Cloud provider |
| `region` | string | Region |
| `instanceId` | unknown | Cloud instance ID |
| `privateIPv4` | unknown | Private IPv4 of the machine |
| `availabilityZone` | unknown | Availability zone of the machine |
| `deploymentCount` | number | The number of deployments |
| `orgId` | unknown | Organization ID |
| `orgName` | unknown | Organization name |
| `createdAt` | string | Creation timestamp |
| `updatedAt` | unknown | Last update timestamp |
| `startedAt` | unknown | Start timestamp |
| `stoppedAt` | unknown | Stop timestamp |
| `error` | unknown | Error message if any |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentuity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
