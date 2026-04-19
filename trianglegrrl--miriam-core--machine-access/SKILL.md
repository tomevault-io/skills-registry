---
name: machine-access
description: SSH access to home network machines (devnuc, Mac Studio, razorback). Use when you need to run commands on other machines or access remote resources. Use when this capability is needed.
metadata:
  author: trianglegrrl
---

# Machine Access Skill

SSH access to machines on the home network.

## Available Machines

### devnuc (Intel NUC) — MY HOME
- **SSH:** `ssh a@192.168.2.24`
- **OS:** Ubuntu 22.04, Linux 5.19
- **Storage:** 915G (67% used)
- **Role:** OpenClaw gateway, home dashboard
- **Note:** This is where I run

### alainasacStudio (Mac Studio) — PRIMARY WORKSTATION
- **SSH:** `ssh alaina@192.168.2.99`
- **OS:** macOS 26.2 (Tahoe)
- **Storage:** 1.8TB (1.3TB free)
- **Ollama:** http://192.168.2.99:11434 (GPU-accelerated)
- **Role:** Main workstation, 54 projects
- **Projects:** ~/projects/

### razorback (Intel NUC, 16 threads) — COMPUTE
- **SSH:** `ssh a@192.168.2.57`
- **OS:** Ubuntu 22.04, Linux 6.8
- **Storage:** 1.8TB NVMe (1.3TB free)
- **Role:** Bioinformatics heavy compute
- **Projects:** BoneVoyage, eveHap, yallHap
- **Neo4j:** bolt://192.168.2.57:7687 (Epstein analysis)

## Quick Reference

```bash
ssh a@192.168.2.24       # devnuc (my home)
ssh alaina@192.168.2.99  # Mac Studio
ssh a@192.168.2.57       # razorback
```

## Usage Notes

- All machines on local network (192.168.2.x)
- SSH keys configured for passwordless access
- Use for: running compute jobs, checking project status, accessing files
- Be careful with: destructive commands, large data transfers during work hours

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/trianglegrrl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
