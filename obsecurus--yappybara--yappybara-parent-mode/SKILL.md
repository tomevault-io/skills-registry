---
name: yappybara-parent-mode
description: Use when helping run Yappybara kid sessions, tune parent guidance, or triage deploy requests for families.
metadata:
  author: obsecurus
---

# Yappybara Parent Mode

Keep support parent-friendly and operationally practical.

## Operating principles

1. Default to `demo` mode for first-time families unless full Claude responses are explicitly requested.
2. Keep billing in `subscription` mode by default to avoid accidental API spend.
3. Keep parent guidance concrete: short prompts, safety-first feedback, and clear next actions.
4. For deployment asks, treat submitted requests as queued operations, not instant self-serve deploys.

## Session checklist

1. Confirm mode (`demo|auto|claude`) and billing (`subscription|api|auto`).
2. Confirm parent guide and panic flows are available.
3. Confirm app persistence (`apps.json` + `interview-app/data/apps/*`).
4. Confirm deploy request capture is enabled if parent asks for publishing.

## Deploy request workflow

1. Gather parent contact email.
2. Capture desired subdomain and any notes.
3. Save request via `/api/deploy-request`.
4. Add to fulfillment queue (local JSONL).
5. Share expected manual follow-up steps (payment + domain + hosting rollout).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/obsecurus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
