---
name: stack-surgeon
description: This skill identifies and resolves system-level deadlocks, process hangs, and resource leaks within the HamClock stack. Use when this capability is needed.
metadata:
  author: ciphernaut
---

# Stack Surgeon Skill
This skill identifies and resolves system-level deadlocks, process hangs, and resource leaks within the HamClock stack.

## Overview
The skill provides "scorched earth" scripts to restore system responsiveness when standard `run_stack.sh stop` commands fail or hang.

## Components
- `scripts/emergency_cleanup.sh`: Forcefully kills all HamClock, Proxy, and Backend processes and releases their network ports.

## Workflows

### 1. Recover from System Freeze
If terminal commands or web interfaces are non-responsive, run this script to clear the environment:
```bash
./skills/stack_surgeon/scripts/emergency_cleanup.sh
```

## Guidelines for Agents
- ONLY use this skill if standard orchestration tools (`run_stack.sh`) are hanging or failing to clear processes.
- After running `emergency_cleanup.sh`, ALWAYS perform a fresh `start all` to ensure a consistent state.
- Check system resources (`free -m`, `df -h`) if hangs recur frequently.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ciphernaut) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
