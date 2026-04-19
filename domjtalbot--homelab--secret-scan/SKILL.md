---
name: secret-scan
description: Run gitleaks scans and triage findings. Use when the user asks: scan for secrets, run gitleaks, secret scan. Use when this capability is needed.
metadata:
  author: domjtalbot
---

# Secret Scan

## Core rules

- Prefer `task secrets:scan` when available.
- If `gitleaks` is missing, ask the user to install it (do not install automatically).
- Do not add allowlists or ignore patterns without explicit user confirmation.
- Summarize findings clearly and suggest fixes or redaction steps.

## Workflow

1) Check whether `gitleaks` is installed or Taskfile has `secrets:scan`.
2) Run the scan.
3) If findings exist, list impacted files and suggest remediation.
4) If false positives, propose a minimal allowlist and ask for approval.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/domjtalbot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
