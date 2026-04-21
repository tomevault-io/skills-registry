---
name: reporting-skills
description: | Use when this capability is needed.
metadata:
  author: campbellmcgregor
---

# Reporting Skills

This skill group generates threat intelligence reports for NOMAD v2.0.

## Available Commands

| Command | Description | Arguments |
|---------|-------------|-----------|
| `/executive-brief` | Leadership summary report | `[timeframe]` |
| `/technical-alert` | SOC/IT alert format | `[threat-id]` |
| `/weekly-summary` | Weekly threat landscape | None |

## Agent Integration

These commands coordinate with NOMAD agents:
- **threat-synthesizer**: Generates personalized reports
- **intelligence-processor**: Provides enriched data
- **truth-verifier**: Validates confidence scores

## Report Types

- **Executive Brief**: Business-focused for leadership
- **Technical Alert**: Detailed for SOC/IT teams
- **Weekly Summary**: Comprehensive weekly analysis

## Trigger Patterns

These commands are auto-suggested when users:
- Ask for reports ("report", "summary", "briefing")
- Mention leadership ("executive", "CEO", "board")
- Need technical documentation ("alert", "SOC", "incident")
- Request periodic summaries ("weekly", "monthly")

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/campbellmcgregor) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
