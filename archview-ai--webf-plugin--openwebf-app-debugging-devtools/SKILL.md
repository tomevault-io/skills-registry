---
name: openwebf-app-debugging-devtools
description: Debug WebF app runtime issues with Chrome DevTools (console/network/elements) and JS-side troubleshooting. Use when the user mentions DevTools console, network tab, stack traces, blank screen, layout measurement issues, or failing network requests. Use when this capability is needed.
metadata:
  author: archview-ai
---

# OpenWebF App: Debugging with DevTools

## Instructions

1. Reproduce and capture signals first: console output, network tab, and any error stack.
2. Use DevTools for:
   - DOM/console debugging
   - network request inspection
   - performance profiling
3. Apply minimal fixes; prefer targeted logging and small repro steps.
4. Use MCP docs to confirm known issues and recommended debugging practices.

If the user’s question is specifically about WebF Go connection/log collection (e.g. `devtools://` or `WEBF_NATIVE_LOG`), prefer `openwebf-app-webf-go`.

If the user’s issue is dev server reachability / LAN IP / HMR websocket problems, prefer `openwebf-app-devserver-network`.

More:
- [reference.md](reference.md)
- [doc-queries.md](doc-queries.md)
- [examples.md](examples.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/archview-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
