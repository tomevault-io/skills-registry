---
name: aida
description: Conversational interface for AIDA (AI-driven smart building automation platform). Use when this capability is needed.
metadata:
  author: openclaw
---
# AIDA Skill for OpenClaw

## Skill Name
aida

## Description
Conversational interface for AIDA (AI-driven smart building automation platform).

## Supported Intents
- aida.status        → Get live building status
- aida.control       → Control devices (lights, shades, HVAC)
- aida.optimize      → Optimize building objectives
- aida.diagnostics  → Run preventive diagnostics

## Example Utterances
- "AIDA, what's the building status?"
- "Optimize for energy savings."
- "Turn off lights on floor 3."
- "Run diagnostics on this zone."

## AIDA Objectives Mapping
- Comfort Optimization
- Energy Optimization
- Preventive Maintenance

## API Expectations
The skill expects AIDA to expose REST endpoints:
- GET /status
- POST /control
- POST /optimize
- GET /diagnostics

All calls are authenticated via bearer token.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
