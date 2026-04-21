---
name: threat-intelligence-skills
description: | Use when this capability is needed.
metadata:
  author: campbellmcgregor
---

# Threat Intelligence Skills

This skill group provides personalized threat intelligence capabilities for NOMAD v2.0.

## Available Commands

| Command | Description | Arguments |
|---------|-------------|-----------|
| `/threats` | Latest personalized threat briefing | None |
| `/critical` | Critical and KEV-listed threats only | None |
| `/cve` | Detailed CVE analysis | `[CVE-ID]` |
| `/crown-jewel` | Threats to specific crown jewels | `[system-name]` (optional) |
| `/trending` | Trending threats and attack vectors | None |

## Agent Integration

These commands coordinate with NOMAD agents:
- **query-handler**: Routes and orchestrates requests
- **threat-collector**: Fetches raw threat data from RSS feeds
- **intelligence-processor**: Enriches with CVSS/EPSS/KEV data
- **truth-verifier**: Validates threat accuracy
- **threat-synthesizer**: Generates personalized responses

## Data Sources

- `config/user-preferences.json` - Organization profile and crown jewels
- `config/threat-sources.json` - Feed configuration
- `data/threats-cache.json` - Cached threat intelligence

## Trigger Patterns

These commands are auto-suggested when users:
- Mention CVE IDs (e.g., "CVE-2024-12345")
- Ask about threats ("what threats", "show me threats", "threat briefing")
- Ask about vulnerabilities ("vulnerability", "critical", "KEV")
- Mention security assets ("crown jewel", "protect my database")
- Ask about trends ("trending", "emerging", "new attack")

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/campbellmcgregor) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
