---
name: writing-bash-scripts
description: Bash script style guide. Always use when writing bash scripts, shell scripts, or CLI bash tools. Use when this capability is needed.
metadata:
  author: lexler
---

# Bash Script Style Guide

- Use `#!/usr/bin/env bash` as shebang
- Always use `set -euo pipefail` for safety
- Keep scripts minimal: no unnecessary comments or echoes
- Minimal input validation; print usage to stderr and exit if inputs are missing/invalid
- Don't check for installed commands if failure is obvious on use
- Make script executable: `chmod +x <script>`
- Prefer concise, direct logic over verbosity

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lexler) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
