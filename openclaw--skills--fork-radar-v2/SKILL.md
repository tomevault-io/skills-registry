---
name: fork-radar-v2
description: AI instruction file for skills by openclaw Use when this capability is needed.
metadata:
  author: openclaw
---
# Fork Radar - Skill Fork Monitor\n\nMonitors GitHub/ClawdHub forks of skills for collabs/backdoors. Scans with molt-security-auditor, PoW verifies, alerts threats/high-score.\n\n## Usage\n- \"Set up fork radar for molt-security-auditor\"\n- cron every=1h: Scan forks, message alerts.\n\n## Workflow\n1. List forks (GitHub API).\n2. Fetch SKILL.md, audit threats.\n3. PoW chain verify.\n4. Score: stars>5, threats=0, PoW valid → collab alert; threats>0 → threat alert.\n\nScripts:\n- radar.js <repo_slug> (e.g., \"danie/molt-security-auditor\")

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
