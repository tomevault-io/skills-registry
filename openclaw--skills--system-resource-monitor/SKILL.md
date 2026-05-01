---
name: system-resource-monitor
description: Use when working with a clean, reliable system resource monitor for CPU load, RAM, Swap, and Disk usage. Optimized for OpenClaw.
metadata:
  author: openclaw
---

# System Resource Monitor

A specialized skill designed to provide concise, real-time server health reports. Unlike bloated alternatives, it uses native system calls for maximum reliability and speed.

## Features
- **CPU Load**: Displays 1, 5, and 15-minute averages.
- **Memory**: Tracks both physical RAM and Swap usage.
- **Disk**: Monitors root partition capacity and percentage.
- **Uptime**: Shows how long your "horse" has been running.

## Usage
Simply ask the agent for "system status", "resource usage", or "server health".
The skill executes the local `./scripts/monitor.sh` script.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
