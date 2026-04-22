---
name: browser-inspect
description: Inspect web pages for privacy, fingerprinting, and content blocker behavior. Use when user asks to 'check this site', 'inspect page', 'analyze trackers', 'test content blocker', or 'debug browser'. Use when this capability is needed.
metadata:
  author: ali
---

# Browser Inspection

## Tool Detection
First, check which tools are available:
```bash
./scripts/check-tools.sh
```

## Preferred: agent-browser CLI
See [AGENT-BROWSER.md](AGENT-BROWSER.md) for refs-based inspection.

## Fallback: Puppeteer MCP
See [PUPPETEER.md](PUPPETEER.md) if MCP is configured.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ali) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
