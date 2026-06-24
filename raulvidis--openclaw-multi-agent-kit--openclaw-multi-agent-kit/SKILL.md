---
name: leadgen-qualification
description: Qualify leads against ICP with transparent scoring and clear next steps. Use when converting raw prospects into prioritized outreach queues. Use when this capability is needed.
metadata:
  author: raulvidis
---

## Install

Copy to `agents/<leadgen>/skills/leadgen-qualification/SKILL.md`. Requires ICP definition in workspace.

# Leadgen Qualification Skill

## Scoring Dimensions

Score each lead 0-100 across:

- ICP fit
- urgency/timing
- budget likelihood
- authority proximity
- technical fit

## Required Output

For each lead provide:

- company + contact
- score + reason
- strongest buying signal
- biggest disqualifier
- recommended next message angle

## Routing

- 80+ → High-priority outreach
- 60-79 → nurture queue
- <60 → archive or re-enrich

---
> Source: [raulvidis/openclaw-multi-agent-kit](https://github.com/raulvidis/openclaw-multi-agent-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
