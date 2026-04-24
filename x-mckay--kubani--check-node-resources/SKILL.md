---
name: check-node-resources
description: > Use when this capability is needed.
metadata:
  author: x-mckay
---

# Check Node Resource Usage

## Preconditions

Before applying this skill, verify:

- Metrics server is available
- Need to understand cluster resource status

## Actions

### 1. Get Resource Usage for All Nodes

Retrieve CPU and memory usage metrics for all nodes.

```yaml
mcp_tool: kubernetes-mcp-server/nodes_top
params: {}
timeout: 30s
```

## Success Criteria

The skill succeeds when:

- [ ] Node resource metrics retrieved successfully
- [ ] Resource pressure identified if present

## Failure Handling

If metrics unavailable:

1. Check if metrics-server is deployed
2. Request prometheus-mcp-server deployment

## Examples

**Input Context:**
```json
{
  "reason": "investigating scheduling issues",
  "suspected_node": "worker-01"
}
```

**Expected Outcome:**
Node metrics retrieved showing CPU and memory usage for each node,
with any nodes under pressure clearly identified.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/x-mckay) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
