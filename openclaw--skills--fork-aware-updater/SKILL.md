---
name: fork-aware-updater
description: AI instruction file for skills by openclaw Use when this capability is needed.
metadata:
  author: openclaw
---
# Fork-Aware Updater\n\nMonitors forks, audits, auto-updates superior forks (score > current). Safety sandbox + confirm.\n\n## Usage\n- \"Update skills from forks\"\n- cron every=6h: exec node skills/fork-aware-updater/updater.js\n\n## Config repos.json\n[{\"slug\": \"danie/molt-security-auditor\", \"threshold\": 10}]\n\n## Workflow\n1. Radar forks.\n2. Audit threats/PoW.\n3. Score fork vs current.\n4. Sandbox test if superior.\n5. Human confirm → git pull/merge.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
