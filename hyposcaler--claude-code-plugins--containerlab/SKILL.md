---
name: containerlab
description: Use when deploying, destroying, inspecting, or interacting with containerlab topologies and SR Linux nodes. Triggers on containerlab, clab, srlinux, topology files, or network lab setup and teardown.
metadata:
  author: hyposcaler
---

# Containerlab Skill

Reference for deploying, inspecting, and tearing down containerlab-based SR Linux labs.

## Environment

- Rootless: all commands run without sudo
- Runtime: Docker (rootless)
- Python tooling: use uv for any Python scripting
- Local execution: labs run on the local machine

## Commands

### Deploy

```bash
containerlab deploy -t <path>.clab.yml
containerlab deploy -t <path>.clab.yml --reconfigure   # destroy lab first, then redeploy clean
containerlab deploy -t <path>.clab.yml --node-filter <node1,node2>
```

### Destroy

```bash
containerlab destroy -t <path>.clab.yml --cleanup
containerlab destroy --name <lab-name> --cleanup
```

Always use --cleanup to remove generated certs and artifacts.

### Inspect

```bash
containerlab inspect --all                    # all running labs
containerlab inspect -t <path>.clab.yml       # specific topology
containerlab inspect --name <lab-name>        # by name
```

Output includes: node name, container ID, image, kind, state, management IPs, mapped ports.

Use --format json | jq to minimize output when scripting or reporting.

Expected JSON structure from `containerlab inspect --format json`:

```json
{
  "<lab-name>": [
    {
      "lab_name": "<lab>",
      "labPath": "<relative-path>",
      "name": "clab-<lab>-<node>",
      "container_id": "<id>",
      "image": "<image>",
      "kind": "<kind>",
      "group": "<group>",
      "state": "running",
      "status": "Up 2 hours",
      "ipv4_address": "<cidr>",
      "ipv6_address": "<cidr>",
      "ports": {},
      "owner": "<user>"
    }
  ]
}
```

Top-level keys are lab names; values are arrays of containers.

### Execute on Nodes

Container naming convention: clab-<lab-name>-<node-name>

```bash
# SR Linux CLI
docker exec -it clab-<lab>-<node> sr_cli "<command>"

# Shell access
docker exec -it clab-<lab>-<node> bash

# SSH (default creds: admin / NokiaSrl1!)
ssh admin@clab-<lab>-<node>
```

#### Using containerlab exec

For multi-node command execution:

```bash
# All nodes in a lab
containerlab exec -t <path>.clab.yml --cmd '<command>'

# Specific node by label
containerlab exec -t <path>.clab.yml --label clab-node-name=<node> --cmd '<command>'

# SR Linux CLI via containerlab exec
containerlab exec -t <path>.clab.yml --cmd 'sr_cli "show version"'
```

## Default Topology

When a quick test lab is needed and the user has no custom topology, create this:

```yaml
name: srlinux-lab
topology:
  nodes:
    srl1:
      kind: nokia_srlinux
      image: ghcr.io/nokia/srlinux:latest
    srl2:
      kind: nokia_srlinux
      image: ghcr.io/nokia/srlinux:latest
  links:
    - endpoints: ["srl1:e1-1", "srl2:e1-1"]
```

### Custom Topologies

When the user provides a .clab.yml:

1. Validate YAML syntax and presence of topology.nodes and topology.links
2. Confirm the SR Linux image tag is pullable
3. If startup-config references files, confirm they exist

Conventions: use .clab.yml extension, place in project root or lab/ directory.

## Operational Procedures

### Deploy Procedure

1. Preflight
   ```bash
   containerlab version
   docker info --format '{{.ServerVersion}}'
   ```
2. Check for conflicts
   ```bash
   containerlab inspect --all --format json 2>/dev/null | jq -r 'keys[]'
   ```
   If same-name lab exists, ask user: redeploy or reuse?
3. Deploy
   ```bash
   containerlab deploy -t <path>.clab.yml
   ```
4. Verify (SR Linux takes 30-60s to boot)
   ```bash
   containerlab inspect -t <path>.clab.yml --format json | jq '[.[][] | {name, state, ipv4: .ipv4_address}]'
   docker exec clab-<lab>-<node> sr_cli "show version" 2>/dev/null
   ```
5. Report: return node names, container names, management IPs, and access instructions.

### Destroy Procedure

1. Identify
   ```bash
   containerlab inspect --all --format json 2>/dev/null | jq -r 'keys[]'
   ```
2. Destroy
   ```bash
   containerlab destroy -t <path>.clab.yml --cleanup
   ```
3. Verify
   ```bash
   containerlab inspect --all --format json 2>/dev/null | jq length
   docker ps --filter "label=containerlab" --format "{{.Names}}" 2>/dev/null
   ```

## SR Linux Reference

### Default Credentials

- User: admin
- Password: NokiaSrl1!

### Common CLI Commands

```bash
sr_cli "show version"
sr_cli "show system information"
sr_cli "show interface brief"
sr_cli "show network-instance default route-table all"
```

### Interface Naming

- Containerlab: e1-1, e1-2, ...
- SR Linux CLI: ethernet-1/1, ethernet-1/2, ...
- Management: mgmt0 (accessible via Docker network)

### Health Check

Use the `state` column from inspect output:

```bash
containerlab inspect -t <path>.clab.yml --format json | jq '[.[][] | {name, state}]'
```

Healthy nodes show `"state": "running"`.

## Context Efficiency

These practices minimize context window consumption when executing commands:

- Always use --format json | jq for inspect commands to extract only what is needed
- Redirect verbose output: containerlab deploy -t ... > /tmp/clab-deploy.log 2>&1
- Use targeted checks over broad scans: if you know the lab name, do not use --all
- Use sr_cli one-liners via docker exec rather than interactive SSH sessions
- Pipe long output to files and read only the relevant parts with head, tail, grep

## Troubleshooting

| Problem | Quick Fix |
|---|---|
| Image pull failure | docker pull ghcr.io/nokia/srlinux:latest, check auth/network |
| Node stuck starting | Check docker logs clab-<lab>-<node>, likely RAM (need ~2GB/node) |
| Cannot SSH | Wait 30-60s post-deploy, verify IP via containerlab inspect |
| Orphan containers after destroy | docker ps -a --filter label=containerlab then docker rm -f |
| Permission denied (rootless) | Verify docker info works, check ~/.config/containers/ |
| Lab name already exists | `containerlab inspect --all --format json \| jq -r 'keys[]'` to check; ask user to redeploy (`--reconfigure`) or destroy first |
| Topology YAML parse error | Validate YAML syntax; check for missing `topology.nodes` or `topology.links` keys |
| Docker daemon not running | Verify with `docker info`; for rootless check `systemctl --user status docker` |
| Node filter matches nothing | Verify node names in topology file match `--node-filter` values exactly |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hyposcaler) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
