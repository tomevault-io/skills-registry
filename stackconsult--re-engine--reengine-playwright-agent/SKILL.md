---
name: reengine-playwright-agent
description: Implement Playwright-based human-browser automation with robust selectors, tracing, retries, and safe human-in-the-loop gates. Use when this capability is needed.
metadata:
  author: stackconsult
---

# Playwright Human-Browser Agent

## Safety
- Never bypass CAPTCHA/2FA.
- Prefer API integrations over UI automation when possible.

## Output artifacts
- traces on failure
- screenshots on failure
- structured logs

## Deliverables
- a Playwright harness with persistent auth strategy
- a self-healing layer (retry taxonomy + fallbacks)
- an abstraction layer usable by MCP tools

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stackconsult) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
