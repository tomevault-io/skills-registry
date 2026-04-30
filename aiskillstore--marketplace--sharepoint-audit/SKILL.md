---
name: sharepoint-audit
description: > Use when this capability is needed.
metadata:
  author: aiskillstore
---

# SharePoint Audit Skill

## When to use
- A user needs to audit SharePoint Online permissions for one site or a CSV-defined batch.
- The operator can run PowerShell 7.4+, Python 3.10+, and PnP.PowerShell locally.

## What to do
1. Ask for: Tenant ID, App (Client) ID, PFX path, internal domains, site URL or CSV, and confirm `PFX_PASS` is set.
2. Run:
   - `pwsh ./sharepoint-audit-agent/agent/powershell/Install-Modules.ps1`
   - `python ./sharepoint-audit-agent/agent/python/audit_agent.py … --output ./runs`
3. On success, show `./runs/<timestamp>/site-*/report.html` and remind the user that the file contains sensitive data.

## Rules
- Only run local commands. Do not fetch from the internet beyond module installs.
- Never echo secrets. Read PFX password from env var.
- Default Sites.Selected scope to **Read**; only pass `--sites-selected-permission Write` if the user explicitly authorizes it.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
