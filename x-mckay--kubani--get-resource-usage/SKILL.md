---
name: get-resource-usage
description: > Use when this capability is needed.
metadata:
  author: x-mckay
---

# Get Resource Usage

## Preconditions

Before applying this skill, verify:

- Metrics server is installed and running
- Cluster has metrics-server or equivalent

## Actions

### 1. Get Node Resource Usage

Retrieve CPU and memory usage for all nodes.

```yaml
mcp_tool: kubernetes-mcp-server/nodes_top
params: {}
timeout: 30s
```

### 2. Get Pod Resource Usage

Retrieve CPU and memory usage for pods.

```yaml
mcp_tool: kubernetes-mcp-server/pods_top
params:
  all_namespaces: true
timeout: 30s
```

### 3. Get Node Stats Summary

Get detailed stats from kubelet for specific node.

```yaml
mcp_tool: kubernetes-mcp-server/nodes_stats_summary
params:
  name: $node_name
timeout: 30s
```

## Success Criteria

The skill succeeds when:

- [ ] Metrics retrieved for all nodes
- [ ] CPU and memory values are valid
- [ ] No errors from metrics API

## Failure Handling

If metrics are unavailable:

1. Check if metrics-server is running
2. Verify node kubelet is responsive
3. Return partial data if some nodes fail

## Examples

**Output:**
```json
{
  "nodes": [
    {
      "name": "node1",
      "cpu_percent": 45,
      "memory_percent": 62,
      "cpu_cores": "2000m",
      "memory_bytes": "4Gi"
    }
  ],
  "top_pods": [
    {
      "name": "vllm-abc123",
      "namespace": "vllm",
      "cpu": "1500m",
      "memory": "16Gi"
    }
  ]
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/x-mckay) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
