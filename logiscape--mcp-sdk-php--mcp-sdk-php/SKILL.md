---
name: bug-review
description: Checks reported bugs in a way that aligns with the project goals. Use when the user reports a possible bug Use when this capability is needed.
metadata:
  author: logiscape
---

A possible (but unverified) bug has been reported in the SDK. Please carefully evaluate if this is a legitimate bug or code quality issue. If there is uncertainty related to alignment with the MCP spec, search the web for the latest version 2025-11-25 of the official MCP spec to clarify. If identified as a legitimate bug or code quality issue, enter plan mode and carefully plan a fix that minimizes risk of introducing regressions or breaking changes while maintaining these two core goals of the SDK: The SDK MUST be capable of running in a standard cPanel/Apache/PHP web hosting environment using PHP 8.1+. The SDK should aim for strict compliance with the official MCP spec, unless an alternate approach is absolutely necessary for compatibility with a standard cPanel/Apache web server.

---
> Source: [logiscape/mcp-sdk-php](https://github.com/logiscape/mcp-sdk-php) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-01 -->
