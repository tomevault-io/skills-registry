---
name: ops-commander
description: DevOps remote management tool for servers. Execute SSH commands, monitor CPU/RAM/Disk, manage Docker containers, maintain server registry. Use when user needs server management, deployment, monitoring, or infrastructure operations. Use when this capability is needed.
metadata:
  author: rikitikitavi2012-debug
---

# Ops Commander — DevOps Management

Remote server management via SSH. Execute commands, monitor resources, manage Docker containers, and maintain server registry.

## Installation

```bash
# Install dependencies
pip install -r requirements.txt

# Or use setup script
bash /a0/usr/skills/setup.sh
```

## When to Use

Use this skill when you need to:
- Execute commands on remote servers via SSH
- Monitor server resources (CPU, RAM, Disk)
- Manage Docker containers remotely
- Deploy applications
- Check server status

## Server Registry

Before using SSH features, add servers to the registry:

```bash
# Add server to registry
python /a0/usr/skills/ops-commander/scripts/ops_commander.py --mode registry --action add --data '{"name":"prod-server","host":"192.168.1.100","user":"admin","key_path":"/root/.ssh/id_rsa"}'
```

Registry file: `/a0/usr/skills/ops-commander/registry.json`

## Usage

### Via Python Script
```bash
python /a0/usr/skills/ops-commander/scripts/ops_commander.py --mode ssh --server prod-server --command "uptime"
```

### Modes

| Mode | Description |
|------|-------------|
| **ssh** | Execute SSH commands |
| **monitor** | Monitor CPU, RAM, Disk usage |
| **docker** | Manage Docker containers |
| **registry** | Add/remove/list servers |
| **status** | Check server connectivity |

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--mode` | str | required | Operation mode |
| `--server` | str | optional | Server name from registry |
| `--command` | str | optional | Command to execute (ssh mode) |
| `--action` | str | optional | Action for docker/registry |
| `--data` | str | optional | JSON data for registry add |
| `--sudo` | flag | false | Run with sudo |

### Examples

1. **Execute command:**
   ```bash
   python /a0/usr/skills/ops-commander/scripts/ops_commander.py --mode ssh --server prod-server --command "df -h"
   ```

2. **Monitor resources:**
   ```bash
   python /a0/usr/skills/ops-commander/scripts/ops_commander.py --mode monitor --server prod-server
   ```

3. **Docker status:**
   ```bash
   python /a0/usr/skills/ops-commander/scripts/ops_commander.py --mode docker --server prod-server --action ps
   ```

4. **Add server:**
   ```bash
   python /a0/usr/skills/ops-commander/scripts/ops_commander.py --mode registry --action add --data '{"name":"web-01","host":"10.0.0.5","user":"deploy","key_path":"/root/.ssh/deploy_key"}'
   ```

## Requirements

- SSH key-based authentication configured
- `paramiko>=3.3.0`

## Security Notes

- Store SSH keys securely (600 permissions)
- Use dedicated deployment keys
- Never commit registry.json with passwords

## Files

```
/a0/usr/skills/ops-commander/
├── scripts/
│   └── ops_commander.py
├── requirements.txt
├── registry.json (created on first use)
└── SKILL.md
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rikitikitavi2012-debug) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
